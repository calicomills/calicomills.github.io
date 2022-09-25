
- <b>Django Models: Overiding the default queryset by using your Custom Manager class</b>

 When you have data that should only be viewed by certain users, you need to have a filtering mechanism that selects only those particular rows in your database table that can be channeled to the front-end.

Here we enhance the Django models.Manager class to further our querying capabilities of the database table. The generic “filter” method is fairly limited when it comes to implementing more complex logic, in this case to display data to a user only if he has the necessary permissions to view it.


          from django.db import models

          from user import User

          class DesignPermissionManager(models.Manager):
                def permissions_match(self, d_permissions, u_permissions):
                    d_permission_list = d_permissions.split(',')
                    for permission in d_permission_list:
                        if permission in u_permissions:
                           continue
                        else:
                           return False
                    return True

                def get_viewable_data(self, user):
                          viewable_refids = []
                          for data in self.all():
                              if self.permissions_match(data.permissions,User.getPermissions(user)):
                                 viewable_refids.append(data.id)
                          return self.filter(ref_id__in=viewable_refids)
        

The method get_viewable_data takes the user, filters the table, and limit it to rows that the specific user has access to.

          class Main_View(View):
                def get(self, request, name):
                    user = str(request.user)
                    data = main_table.objects.get_viewable_data(user) \
                                             .order_by('-name') \
                                             .values()



In views.py which powers the front-end in Django, we can easily get the user info from the request object and pass it to our filtering method , and thus restrict the user to the data he has permission to.


[linkedin]:https://www.linkedin.com/in/jishnuck/ 
[Mail]:jishnuck26@gmail.com

