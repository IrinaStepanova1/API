# API
#from django.db import models
'''
class News(models.Model):
    title = models.CharField(max_length=32)
    date = models.DateField()
    body = models.CharField(max_length=32)
    deleted =  models.Bool( news=False, null=False)

    def __unicode__(self):
        return self.title + ' (' + self.date + ')' + self.body


class Comments(models.Model):
    news = models.ForeignKey(News, null=False)
    title = models.CharField(max_length=32)
    date = models.DateField()
    comment = models.CharField(max_length=32)

    def __unicode__(self):
        return self.title + ' (' + self.news.id + ')'
'''

'''from tastypie.resources import ModelResource
from App.models import News, Comments


class NewsResource(ModelResource):
    class Meta:
        queryset = News.objects.all()
        resource_name = 'news'


class CommentResource(ModelResource):
    news = fields.ForeignKey(NewResources, 'news', null=True)
    class Meta:
        queryset = Comment.objects.all()
        resource_name = 'comment'
        '''

'''from django.conf.urls.defaults import *
from tastypie.api import Api
from api.resources import CommentResource, NewsResource

v1_api = Api(api_name='v1')
v1_api.register(CommentResource())
v1_api.register(NewsResource())

urlpatterns = patterns('',
    (r'^api/', include(v1_api.urls)),
)'''
