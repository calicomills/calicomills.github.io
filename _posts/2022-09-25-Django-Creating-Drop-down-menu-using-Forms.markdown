
Liquid::Template.register_tag('raw', Jekyll::RawTag)Forms are an integral part of Django, and it facilitates the framework to accept inputs from the user in a safe and secure manner. 
It allows one to restrict the type and size of data entered, thus acting as an initial filter layer before the data reaches the back-end. 
This helps reining in the post processing to be done and reduces the chance of errors creeping in.

Here I explain how to render a drop down menu (shown below) which consist of a list of indexes queried from a database or a predefined 
list to the front-end.

https://user-images.githubusercontent.com/24970675/192156889-8e52e00d-c73b-47a9-995d-b4be9e4b109f.png


Firstly, we create the model for the form field in models.py which would be used to model the form object in forms.py , 
here you can also specify various restrictions on the form fields.


     class Label(models.Model):
          label = models.CharField(max_length=50,null=False)
          label_ref = models.CharField(max_length=50,null=False)
          
Now in forms.py we override the LabelForm class, so that we are able to customize the data that goes into the label and label_ref fields.


    class LabelForm(forms.ModelForm):
            class Meta:
                      model = Label
                      fields = ['label','label_ref']

            def __init__(self,  *args, **kwargs):
                self.param_name = kwargs.pop('param_name')
                self.new_table = kwargs.pop('new_table')
                self.ref_table =  kwargs.pop('ref_table')
                super(LabelForm, self).__init__(*args, **kwargs)
                self.fields['label'].queryset = self.new_table.objects \
                                                            .filter(param_name__startswith=self.param_name) \
                                                            .order_by('label') 
                self.fields['label_ref'].queryset = self.ref_table.objects \
                                                                .filter(param_name__startswith=self.param_name) \
                                                                .order_by('-label') 
                self.fields['label'].label ="New Label"
                self.fields['label_ref'].label ="Reference Label"

            label = MyModelChoiceField(queryset=None, error_messages={
                                               'invalid_choice': ('Select a valid choice')
                                                }, to_field_name="label")
            label_ref =  MyModelChoiceField(queryset=None,error_messages= {
                                                    'invalid_choice': ('Select a valid choice')
                                                     }, to_field_name="label_ref")
                               
                               
                               
   In views.py , we can provide different types of data and instantiate the LabelForm class to suit our requirements.
   These inputs can be used to query all types of data from the database , and can be passed on to different fields in the front-end through a queryset. 
   This feature greatly enhances the dynamic nature of the front-end, as this can be use to render different type of forms to different users or on 
   basis of other inputs.

      form =  LabelForm(initial={ 'label': latest_run_label,
                                  'label_ref': latest_run_label,
                                  'choices':(latest_label,latest_label)},
                                  param_name=param_name,new_table=db_class,ref_table=db_class)
                                  
                                  
  The html code used to render the form is shown below, the form object is passed to the Django template using the django.shortcuts.render() in views.py.

  {% raw %}
        <form action="" method="post">
              <ul>
                  <li>
                      {{ form.label.label }}
                      {{ form.label }}
                      {{ form.label_ref.label }}
                      {{ form.label_ref }}
                 </li>
          </form>
{% endraw%}
