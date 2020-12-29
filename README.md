# DjangoCodeSummary
Code Summary for Tech Academy Python live project

## Introduction
After finishing up the Python coursework at the Tech Academy, I worked on a team for two weeks developing a web application utilizing Django Framework. The application consists of a wide array of different hobbies, in which each sub-app focuses on a different hobby of choice. 
My app focused on hiking. I created a database for hiking trails, which allowed the user to create, edit, store, and delete trails from anywhere in the US. Trails are displayed with their name, location, length, level of difficulty, camping ability, an image, and a description. I also included a search feature, allowing the user to search for trails by name or location. 
This was a great opportunity to learn the Django Framework, and to become accustomed to solving and debugging issues. 

## Building an App
* Home Page
To start, I created an app through Django and registered it within the main project. I created my base and home templates, then added a function to render these pages. Lastly, I created a urls file and registered it within the main app. 

## Creating a Model and Form
* Model
* Form
* HTML page
I created a Trails model and defined its characteristics- name, location, difficulty, length, camping, image, description. For location, I provided an alphabetically ordered tuple of states to choose from. For difficulty, I provided a tuple of 'Easy', 'Medium', and 'Hard'. A tuple was the best choice, since it maintained a set order of the items.
To create a user input form, I created a new forms.py file and utilized Django widgets to display choices on the user end. I made a template page for the form, then I created a views function to render this page and connected it in my urls file.

### Form views function
```python
def create_trail(request):
    form = TrailForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('HikingApp_addtrail')
    else:
        print(form.errors)
        form = TrailForm()
    context = {
        'form': form,
    }
    return render(request, 'HikingApp/HikingApp_form.html', context)
```

## Displaying Items in the Database
* Index Page
* Search Results
* Template Code
I created a template page to display all the trails in the database, and linked the trail name to its details. I included pagination to limit the amount of trails to 10 per page, and also included a search bar to find trails by name or location. The search bar lead to a new template page, displaying items in the database that matched the user's input for location or name. 
### Views function for pagination and rendering index
```python
def display_index(request):
    trail_list = Trails.objects.order_by('location').all()
    page = request.GET.get('page', 1)

    paginator = Paginator(trail_list, 10)  # show 10 trails per page
    try:
        trails = paginator.page(page)
    except PageNotAnInteger:
        trails = paginator.page(1)
    except EmptyPage:
        trails = paginator.page(paginator.num_pages)
    return render(request, 'HikingApp/HikingApp_index.html', {'trails': trails})
```

### Index Paginator Template
```
{% if trails.has_other_pages %}
  <ul class="pagination">
    {% if trails.has_previous %}
      <li><a href="?page={{ trails.previous_page_number }}">&laquo;</a></li>
    {% else %}
      <li class="disabled"><span>&laquo;</span></li>
    {% endif %}
    {% for i in trails.paginator.page_range %}
      {% if trails.number == i %}
        <li class="active"><span>{{ i }} <span class="sr-only"></span></span></li>
      {% else %}
        <li><a href="?page={{ i }}">{{ i }}</a></li>
      {% endif %}
    {% endfor %}
    {% if trails.has_next %}
      <li><a href="?page={{ trails.next_page_number }}">&raquo;</a></li>
    {% else %}
      <li class="disabled"><span>&raquo;</span></li>
    {% endif %}
  </ul>
{% endif %}
```

### Searchbox Function
```python
def search_results(request):
    if request.method == 'GET':
        query = request.GET.get('q')

        submitbutton = request.GET.get('submit')

        if query is not None:
            lookups = Q(name__icontains=query) | Q(
                location__icontains=query)  # can lookup trail by name or location only

            results = Trails.objects.order_by('name').filter(
                lookups).distinct()  # distinct() is to avoid duplicate results

            context = {'results': results,
                       'submitbutton': submitbutton}

            return render(request, 'HikingApp/HikingApp_searchresults.html', context)

        else:
            return render(request, 'HikingApp/HikingApp_searchresults.html')

    else:
        return render(request, 'HikingApp/HikingApp_searchresults.html')
```

## Displaying Details and Edit/Delete Functions
* Details Page
* Edit/Delete
