# Live Project Summary

## Introduction

During the past two weeks I worked with fellow students to create a website called "The Spacebar" written in Django. It was in the beginning stages of development, so a lot of stories that were tasked of us were to create new apps for the project such as creating an application that would show a menu or an application that would display a sun tracker. This was an amazing learning experience to be able to work on code that was being used and implemented while others were working on similar applications. We were able to ask questions of each other and help each other out when one person had the solution to a problem someone else was stuck on. This process help us to hit real world errors and learn how to approach them alone and together. Below are descriptions of the stories I worked on during the two-week sprint.

## Menu App Story
I created an app within the Django framework that work reflect the list of Menu items and give the ability to create a new menu item. In order to do this I had to create a new model for the menu items:

    class Product(models.Model):
        type = models.CharField(max_length=60, choices=CATEGORIES)
        name = models.CharField(max_length=60, default="", blank=True, null=False)
        description = models.TextField(max_length=300, default="", blank=True)
        price = models.DecimalField(default=0.0, max_digits=10000, decimal_places=2)
        objects = models.Manager()

I created a page that you could add an Menu item to the database:

    <div class="container">
        <form method="POST" action="{% url 'menu:create' %}">
            <div class="row">
                <!--Cross Site Request Forgery (csrf_token) protection -->
                {% csrf_token %}
                {{ form.non_field_errors }}
                {{ form.as_p }}
            </div>
            <div class="container">
                <input type="submit" class="btn btn-primary" value="Create New Item" name="Save_Item">
                <a href="{% url 'menu:index' %}"><button type="button" class="btn btn-secondary" value="Cancel">Cancel</button></a>
            </div>
        </form> 
    </div>

In order to execute this I wrote a create method with the views.py file. 

    def create(request):
        form = ProductForm(request.POST or None)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect('/menu/index/')
        else:
            print(form.errors)
            form = ProductForm()
        context = {
            'form': form,
        } 
        return render(request, 'menu/menu_create.html', context)


## Astronomy Object Finder App Story

This story used the integration of an outside API into our framework. The goal was to create a page where a user could enter a astrological object and it would then give the user a table of data about that object where this data is coming from an outside API from this [link](https://api.le-systeme-solaire.net/). 

I first had to create a method in the views.py file to allow the data from the API to be accessed from the webpage:

    def objectFinder(request):
        body = {}
        if 'body_id' in request.GET:
            body_id = request.GET['body_id']
            try:
                url = 'https://api.le-systeme-solaire.net/rest.php/bodies/%s' % body_id
                response = requests.get(url)
                search_was_successful = (response.status_code == 200)  # 200 = SUCCESS
                body = response.json()
                body['success'] = search_was_successful
            except:
                body['message'] = 'Sorry, "%s" has no results. Please check the spelling.' % body_id
                body['success'] = False
        return render(request, 'objectFinder/objectFinder_index.html', {'body': body})


Then I had to format the webpage to be able to grab the input from the user, then take the data that we wanted to output an display it in an organized way:

    <h1 class="mx-auto my-0 text-uppercase">The Spacebar</h1>
    <h1>Astronomy Object Search</h1>
    <form method="get">
        <input type="text" name="body_id">
        <button type="submit">Search</button>
    </form>
    {% if body %}
        <br>
        {% if body.success %}
            <table class="table table-borderless table-dark">
                <thead>
                <tr>
                    <th scope="col">Body Name</th>
                    <th scope="col">{{ body.englishName }}</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <th scope="row">Is it a Planet?</th>
                    <td>{{ body.isPlanet }}</td>
                </tr>
                <tr>
                    <th scope="row">Dimension</th>
                    <td>{{ body.dimension }}</td>
                </tr>
                <tr>
                    <th scope="row">Gravity</th>
                    <td>{{ body.gravity }}</td>
                </tr>
                <tr>
                    <th scope="row">Mean Radius</th>
                    <td>{{ body.meanRadius }}</td>
                </tr>
                </tbody>
            </table>
        {% else %}
            <p class="lead" style="color:white">{{ body.message }}</p>
        {% endif %}
    {% endif %}

## Skills I Acquired

1. I became very familiar with the Django framework and how to navigate all the files.
2. I was able to work with other people using git (a skill that is hard to complete when you are learning on your own)
3. I became familiar with the sprint style of project management and was able to ensure communication throughtout the team because of this tool.
4. I furthered my capability to research exactly what I am trying to look for and ask questions when necessary. 
5. I got a great amount of experience troubleshooting my issues with people remotely and trying to figure out how to best communicate over the tools being used.
