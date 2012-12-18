---
layout: post
title: If you have REST, why XML-RPC?
---
 
## Update

There's points going back and forth on [Hacker News](http://news.ycombinator.com/item?id=1052926) today -- I don't know if HN archives its threads forever, so if that link breaks let me know.


## The original article follows

There's a large re-architecture project for one of our products at work that's in the planning stages, and we're trying to decide between XML-RPC and REST for communication between two layers. The title of the post should make my bias pretty obvious.

## XML-RPC

XML-RPC is just a protocol that defines how you can call methods on a remote system. It works by serializing your request and any parameters into a standard chunk of XML, and similarly encoding the response and data from the server. Its up to the client and server to agree upon what methods are available and what parameters they take, although this can be facilitated by providing that data as an XML file that clients can consume, which most implementations auto-generate for you. It also provides for standard responses that indicate error codes and descriptions, which are up to the implementor to create.

## Wait, what's wrong with that?

It's completely unnecessary. We're building a web service, which runs over HTTP. HTTP already has facilities for making requests and returning responses. Plus, its error states are already well known, and it provides for other things, such as client-side caching and authentication, that XML-RPC doesn't.

## So what's REST?

Pretty much that. REST over HTTP says you use all the things HTTP gives you, and you provide representations of your resources. You do this with the Web all day: when you go to someone's Twitter profile, for instance, Twitter is giving you an HTML representation of a resource they have, namely a Twitter user.

## But what's the point?

Let's say you know I provide Employee information for a company, and you need to get information for a specific employee.  You've come to my site, and read the documentation for my service so you know how to interact with it.

### With XML-RPC

In your programming language of choice, fire up an appropriate library and have it read in my auto-generated XML description of my service. Then, call the `SearchEmployeesByName()` method and give it the employee's name. Your library will create the XML for the request, decode the XML from the response, and likely give you a native object that you can manipulate.  You can then find the employee you want and call `GetEmployeeDetailsById()` and pass the specific employee ID you found in step one. 

### With REST

If you just need some data, open your web browser and go to `http://my-fake-service.com/employees/`.  Click the employee name you want, which might take you to `/employees/121`.  I've provided you the HTML representation of the resource (a web page). You're done.

Assuming you want to access the data programmatically, make an HTTP request to `/employees.json` and get a JSON representation of my employees. Then, once you've have the ID, make a second request to `/employees/121.json`, and decode the JSON response.

That's not all. If you need an image of the employee, try `/employees/121.jpg`. If you need the data as a PDF, try `/employees/121.pdf`.  REST isn't limited to providing XML responses, it can provide any format the server supports.  

And it's easy to provide a list of related links to other representations or related resources.

## Why wouldn't anyone like that?

No clue. During an impassioned speech, I got a lot of questions or statements, usually things that XML-RPC handles that REST seemingly doesn't.

<dl>
	<dt>How do you know if an error occurred?</dt>
	<dd>HTTP provides statuses for nearly any error you can encounter. You probably know about "404 Document Not Found"; there's <a href="http://en.wikipedia.org/wiki/List_of_HTTP_status_codes">tons more</a>, including "400 Bad Request" (I don't understand what you want), "401 Unauthorized" (You need to authenticate first), and more esoteric but useful codes such as "201 Created" (We've created a resource based on your input), "402 Payment Required" (We need money for this), and "409 Conflict" (Someone else changed this resource before you did, you should try again).  With XML-RPC you have to re-invent all of these.</dd>
	
	<dt>XML-RPC provides automatic documentation.</dt>
	<dd>Well, your library automatically generates documentation like "getUser() takes an integer and returns a UserStruct" -- you still have to define what a UserStruct is and what that integer means. Kudos if your library can automatically generate those, but now we're discussing specific implementations, which is a separate issue.</dd>
	
	<dt>How do you know if your data's valid?</dt>
	<dd>You test it, same way you do with XML-RPC. Someone actually asked how you know a date is a date if the XML doesn't specifically tell you it's a date. If I'm providing a piece of data called "expiration_date" and it's <em>not</em> a date, that's something the client needs to handle regardless of how it obtained it.</dd>
	
	<dt>REST seems too restrictive.</dt>
	<dd>REST doesn't force you to always talk about representations of resources, but you gain a lot of simplicity if you do. For example, you can have a "Dashboard" resource who's representation is as a bunch of overview data in whatever format.<br><br>One of our issues was "I want to provide a count of a particular resource instead of returning the whole list." You can absolutely do that: if you have /employees.json that provides a JSON list of all employees, knock a parameter on there: /employees.json?count=true -- it seems unclean, but all the logic for retrieving employee data stays in one place, instead of scattered around multiple methods like "GetEmployees()", "GetEmployeeCount()", "GetActiveEmployeeCount()" and so on.</dd>

	<dt>But all our clients use XML-RPC!</dt>
	<dd>Touché.</dd>
</dl>

## Clients, The Business Kind

If the field you're working in is dominated by companies that have (for whatever reasons) standardized on things like XML-RPC and SOAP, and you want to integrate with them, it's probably best to use XML-RPC. I like to think that REST provides *obvious* advantages over XML-RPC as described above, but in the end if the people giving you money want XML-RPC, then you give them XML-RPC.

Responses to this are encouraged, and quality or thought-provoking responses may be added. [Twitter](http://twitter.com/joncanady) or [email](mailto:jon@joncanady.com) your thoughts.