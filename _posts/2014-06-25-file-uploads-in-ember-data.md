---
layout: post
title: "File Uploads in Ember Data"
category: EmberJS
---

Recently I've been working on an EmberJS application ([SalesFlip](http://www.salesflip.com) - go register there now) which
needs to allow file uploads. There are a couple of different approaches to this
which I'll discuss here, along with a new one which I would like to propose. If
you are wondering what all the import and export statements are in the examples
below, it's because I'm using [Ember App
Kit](http://iamstef.net/ember-app-kit/). You should use it too!


## HTML5 FileReader API
[https://developer.mozilla.org/en-US/docs/Web/API/FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader)

The HTML5 FileReader API is fine for really small file uploads outside of
models. It seems to be the
[currently](http://stackoverflow.com/questions/19909267/ember-js-value-binding-with-html5-file-upload)
[recommended](http://stackoverflow.com/questions/9200000/file-upload-with-ember-data?lq=1)
[solution](http://stackoverflow.com/questions/13923122/ember-js-upload-file-component?lq=1)
[on StackOverflow](http://stackoverflow.com/questions/14676546/how-file-upload-with-ember-js?lq=1).
Just create an UploadFile view and then bind the file attribute to the
relevant attribute in the model. However, the ember-data adapter will then
upload the file as a [data URI](http://en.wikipedia.org/wiki/Data_URI_scheme) via JSON.
This proved pretty much useless for me. Anything over around 60k would fail.

app/models/user.coffee
{% highlight coffeescript %}
User = DS.Model.extend
    avatar: DS.attr('string')

`export default User`
{% endhighlight %}

app/routes/users/new.coffee
{% highlight coffeescript %}
UsersNewRoute = Ember.Route.extend
    model: ->
        @store.createRecord('user')

    actions:
        save: ->
            @currentModel.save()

`export default UsersNewRoute`
{% endhighlight %}

app/templates/users/new.hbs
{% highlight html %}
view "upload-file" name="avatar" fileBinding="avatar"
{% endhighlight %}

app/views/upload_file.coffee
{% highlight coffeescript %}
UploadFile = Ember.TextField.extend
    tagName: 'input'
    attributeBindings: ['name']
    type: 'file'
    file: null
    change: (e) =>
        reader = new FileReader()
        reader.onload = (e) =>
            fileToUpload = e.target.result
            Ember.run =>
                @set 'file', fileToUpload
        reader.readAsDataURL(e.target.files[0])

`export default UploadFile`
{% endhighlight %}

## Custom FormData Upload View

With this approach ([adapted from here](http://stackoverflow.com/questions/19620122/ember-with-jquery-file-upload))
we define an upload button view which will upload the file on
change. This would require some backend magic to work for new users (ones
without an ID). Perhaps the file could be saved and then associated with the
user when the user is saved.

app/templates/users/new.hbs
{% highlight html %}
view "upload-button" userBinding="user"
{% endhighlight %}

app/views/upload_button.coffee
{% highlight coffeescript %}
UploadButton = Ember.View.extend
    tagName: 'input'
    attributeBindings: ['type']
    type: 'file'
    originalText: 'Upload Finished Product'
    uploadingText: 'Busy Uploading...'

    newItemHandler: (data) ->
        @get('controller.store').push('item', data)

    preUpload: ->
        parent = @.$().closest('.fileupload-addbutton')
        upload = @get('uploadingText')

        parent.addClass('disabled')
        @.$().css('cursor', 'default')
        @.$().attr('disabled', 'disabled')

    postUpload: ->
        parent = @.$().closest('.fileupload-addbutton')
        form = parent.closest('#fake_form_for_reset')[0]
        orig = @get('originalText')

        parent.removeClass('disabled')
        @.$().css('cursor', 'pointer')
        @.$().removeAttr('disabled')
        form.reset()

    change: (e) ->
        formData = new FormData()
        @preUpload()
        formData.append('user_id', @get('user.id'))
        formData.append('file', @.$().get(0).files[0])
        $.ajax
            url: '/file_upload_handler/'
            type: 'POST'
            # Ajax events
            success: (data) =>
                @postUpload()
                @newItemHandler(data)
            error: =>
                @postUpload()
                alert('Failure')
            # Form data
            data: formData
            # Options to tell jQuery not to process data or worry about content-type.
            cache: false
            contentType: false
            processData: false

`export default UploadButton`
{% endhighlight %}

## Ember Data FormData Adapter

This is the approach that I have settled on. I created
a [FormData](https://developer.mozilla.org/en-US/docs/Web/Guide/Using_FormData_Objects#Sending_files_using_a_FormData_object)
Adapter which is used by any models which require file upload. Using this
approach, adding file uploads to another file is just a matter of creating a new
adapter for it which extends the FileUpload adapter. Although I haven't tried
it, it should be easy to combine this approach with the HTML5
File Reader approach to generate image previews and validate file information
before uploading.

First define a transform so that files are left untouched

app/transforms/file.coffee
{% highlight coffeescript %}
FileTransform = DS.Transform.extend
  serialize: (jsonData) ->
    jsonData

  deserialize: (externalData) ->
    externalData

`export default FileTransform`
{% endhighlight %}


Use the newly defined "file" attribute type

app/models/user.coffee
{% highlight coffeescript %}
User = DS.Model.extend
    avatar: DS.attr('file')

`export default User`
{% endhighlight %}

Create a FormDataAdapter. This is what handles uploading the data via FormData
instead of the default JSON.

app/adapters/form_data.coffee
{% highlight coffeescript %}
`import ApplicationAdapter from 'appkit/adapters/application'`

get = Ember.get

FormDataAdapter = ApplicationAdapter.extend
  ajaxOptions: (url, type, hash) ->
    hash = hash || {}
    hash.url = url
    hash.type = type
    hash.dataType = 'json'
    hash.context = @

    if hash.data and type != 'GET' and type != 'DELETE'
      hash.processData = false
      hash.contentType = false
      fd = new FormData()
      root = Object.keys(hash.data)[0]
      for key in Object.keys(hash.data[root])
        if hash.data[root][key]
          fd.append("#{root}[#{key}]", hash.data[root][key])

      hash.data = fd

    headers = get(@, 'headers')
    if headers != undefined
      hash.beforeSend = (xhr) ->
        for key in Ember.keys(headers)
          xhr.setRequestHeader(key, headers[key])

    hash

`export default FormDataAdapter`
{% endhighlight %}

Create a UserAdapter for the User model to use

app/adapters/user.coffee
{% highlight coffeescript %}
`import FormDataAdapter from 'appkit/adapters/form_data'`

UserAdapter = FormDataAdapter.extend()

`export default UserAdapter`
{% endhighlight %}