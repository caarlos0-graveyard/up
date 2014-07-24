---
layout: post
title: "Ember CLI Bootstrap Modals"
category: ruby
---

Here's how to integrate bootstrap modals into EmberJS quickly and easily.
It's really easy to create reusable modal code to integrate
[Twitter Bootstrap modals](http://getbootstrap.com/javascript/#modals) into
[EmberJS](http://emberjs.com/) projects. Here's how to do it:

## TL;DR
Demo at
[http://ember-cli-bootstrap-modal.herokuapp.com](http://ember-cli-bootstrap-modal.herokuapp.com/)

Code at
[https://github.com/mattbeedle/ember-cli-bootstrap-modal-example](https://github.com/mattbeedle/ember-cli-bootstrap-modal-example)

## The Modal code

The modal code is all the code that our modals will be built on top
of. It consists of a ```ModalController```, ```ModalView```, ```openModal``` and
```closeModal``` actions, and a ```modal``` outlet. Links to demo app and
working code are at the bottom of the article.


First, let's create a basic application template with a modal outlet into which
our modals will be rendered:

```app/templates/application.hbs```
{% highlight html %}
{% raw %}
<h2 id='title'>Bootstrap Modal with Ember CLI examples</h2>

{{outlet}}

{{outlet "modal"}}
{% endraw %}
{% endhighlight %}


Now we will define a ```ModalView```. All modal views will inherit from this
view. It ensures that modal settings are consistent across all modals.

```app/views/modal.coffee```
{% highlight coffeescript %}
`import Ember from 'ember'`

ModalView = Ember.View.extend
  tagName: 'div'
  classNames: 'modal'.w()

  # didInsertElement is run once the element of the view has been inserted into
  # the DOM
  # http://emberjs.com/api/classes/Ember.View.html#event_didInsertElement
  didInsertElement: ->
    # First set the div id to "modal". "@$()" is the shortcut to get the view's
    # jQuery element
    @$().attr('id', 'modal')

    # Now set up the modal
    @$().modal
      keyboard: false
      backdrop: 'static'

    # Now show the modal
    @$().modal('show')

  # This is run when the element of the view is going to be destroyed. We
  # override it here to hide the modal first.
  # http://emberjs.com/api/classes/Ember.View.html#event_willDestroyElement
  willDestroyElement: ->
    @$().modal 'hide'

`export default ModalView`
{% endhighlight %}


Now we define a ```ModalController```. This will handle the cancel action. All other
modal controllers will inherit from this.

```app/controllers/modal.coffee```
{% highlight coffeescript %}
`import Ember from 'ember'`

ModalController = Ember.ObjectController.extend
  actions:
    cancel: ->
      # If the controller @content is set, then we need to roll it back to avoid
      # inconsistent state between client and server.
      @content.rollback() if @content

      # Now we send "closeModal" event to bubble up to the application route
      @send 'closeModal'

`export default ModalController`
{% endhighlight %}


Now we need to define the global ```openModal``` and ```closeModal``` actions.
We will do this in ```ApplicationRoute``` as everything bubbles up to here in
the end.

```app/routes/application.coffee```
{% highlight coffeescript %}
`import Ember from 'ember'`

ApplicationRoute = Ember.Route.extend
  actions:
    # This takes one argument; The template to render. It then renders it into
    # The modal outlet which we defined in the application template.
    openModal: (modal) ->
      @render modal,
        into: 'application'
        outlet: 'modal'

    # This handles removing the modal. It simply disconnects the modal outlet to
    # remove the modal. When this happens the "willDestroyElement" function will
    # be called on the ModalView, telling it to close the modal.
    closeModal: ->
      @disconnectOutlet
        outlet: 'modal'
        parentView: 'application'

`export default ApplicationRoute`
{% endhighlight %}


## Building our first modal

At this point all of the code to handle modals is complete. Now we can actually
create our first modal. Let's do this with an action fired from our index
template.

```app/templates/index.hbs```
{% highlight html %}
{% raw %}
<p><a href="" {{action openConfirmationModal on="click"}}>Confirmation Modal</a></p>
{% endraw %}
{% endhighlight %}


We will create an ```IndexRoute``` to handle the ```openConfirmationModal```
action.

```app/routes/index.coffee```
{% highlight coffeescript %}
`import Ember from 'ember'`

IndexRoute = Ember.Route.extend
  actions:
    openConfirmationModal: ->
      # The "openModal" event will bubble up to the ApplicationRoute and be
      # handled there.
      @send 'openModal', 'confirmation/new'

`export default IndexRoute`
{% endhighlight %}


We will need a confirmation/new template now to be rendered.

```app/templates/confirmation/new.hbs```
{% highlight html %}
{% raw %}
<div class="modal-dialog">
  <div class="modal-content">
    <div class="modal-header">
      <button class="close" type="button" {{action cancel on="click"}}>x</button>
      <h4 class="modal-title">Confirm</h4>
    </div>
    <div class="modal-body">
      Are you sure about this?!?!
    </div>
    <div class="modal-footer">
      <button class="btn btn-default" {{action cancel on="click"}}>Nope</a>
      <button class="btn btn-danger" {{action confirm on="click"}}>Yes, do it!</a>
    </div>
  </div>
</div>
{% endraw %}
{% endhighlight %}


And finally we will need to create a ```ConfirmationNewController``` to handle
the events from the confirmation/new template.

```app/controllers/confirmation/new.coffee```
{% highlight coffeescript %}
`import ModalController from 'ember-cli-bootstrap-modal-example/controllers/modal'`

# We inherit from ModalController to get the "cancel" action.
ConfirmationNewController = ModalController.extend
  actions:
    confirm: ->
      alert 'OK, it will be done!'
      @send 'closeModal'

`export default ConfirmationNewController`
{% endhighlight %}

## Notes & links

So that's it. Simple Bootstrap modals in EmberJS. I've created a demo app and
published it to GitHub. It includes a confirm modal, information modal and
a form modal (using ember-easyForm, ember-validations and ember-data
LocalStorage).

Demo at
[http://ember-cli-bootstrap-modal.herokuapp.com](http://ember-cli-bootstrap-modal.herokuapp.com/)

Code at
[https://github.com/mattbeedle/ember-cli-bootstrap-modal-example](https://github.com/mattbeedle/ember-cli-bootstrap-modal-example)