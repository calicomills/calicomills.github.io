Imagine a platform that would let you build analytics dashboards or a website like shopify.com, how do they build the database tables, ORMs and the necessary backend functionality required to power your specific website. For a start in Django, if we give the user the ability to pick the fields he wants in his dashboard, to implement that feature completely we need also make those entries in models.py.

The following is a start/attempt to render models.py taking the fields as input from the user.(Also handles the Meta class attributes.)

    class ClassEngine:
        def __init__(self):
            pass
        def class_generator(self, class_name, **kwargs):  
            yield f"class {class_name}: \n"
            meta_class_req = False
            meta_fields = []
            if kwargs.get('meta_fields', None):
                meta_class_req = True
                meta_fields = kwargs.pop('meta_fields')

            for key in kwargs:
                if key not in meta_fields:
                    yield f"\t{key} = {kwargs[key]}\n"

            if meta_class_req:
                yield f"\tclass Meta: \n"
                for field in meta_fields:
                    yield f"\t\t{field} = {kwargs[field]}\n"


    class FileEngine:
        IMPORTS = {'models.py' : ['from django.db import models']}
        def __init__(self):
            pass
        def import_generator(self, file_name):
            for import_st in self.IMPORTS[file_name]:
                yield f"{import_st} \n"

Now, to create the models.py with the necessary input fields.

    from dynamic_django import FileEngine, ClassEngine

    fg = FileEngine().import_generator('models.py')
    cg = ClassEngine().class_generator("ModelClass", ref_id = "models.CharField()", fields = ['ref_id'],
                                       value = "models.IntegerField()", meta_fields = ['fields'])


    with open("models.py", 'w') as f:
        for line in fg:
            f.write(line)

        for line in cg:
            f.write(line)
            
Similarly I believe we would be able to generate all the other necessary files which are required for a Django app dynamically, without going to the pain of manually generating all the files. #DRY

The generated models.py:

    from django.db import models 
    class ModelClass: 
      ref_id = Models.CharField()
      value = Models.IntegerField()
      class Meta: 
        fields = ['ref_id']
        
   

         
