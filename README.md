# API
# -*- coding: utf-8 -*-
from aiohttp import a
from datetime import datetime
import json 

class News():

    __tablename__ = 'news'

    id = a(primary_key=True)
    title = a.match_info.get( "news_1")
    date = a.match_info.get("2019-01-01T20:56:35")
    body = a.match_info.get("The news")
    deleted = a.match_info.get( news=False, null=False)

    def __init__(self, title, date, body ,deleted):

        self.title = title
        self.date = date
        self.body = body
        self.deleted = deleted


    @classmethod
    def from_json(cls, data):
        return cls(**data)

    def to_json(self):
        to_serialize = ['id', 'title', 'date', 'body', 'deleted']
        d = {}
        for attr_name in to_serialize:
            d[attr_name] = getattr(self, attr_name)
        return d

class Comments():

    __tablename__ = 'comments'

    id = a(primary_key=True)
    news = a.ForeignKey(News, null=False)
    title = a.match_info.get( "comment_1")
    date = a.match_info.get("2019-01-02T21:58:25")
    comment = a.match_info.get("Comment")

    def __init__(self, id, news, title, date, comment):

        self.news = news
        self.title = title
        self.date = date
        self.comment = comment


    @classmethod
    def from_json(cls, data):
        return cls(**data)

    def to_json(self):
        to_serialize = ['id', 'news', 'title', 'date', 'comment']
        b = {}
        for attr_name in to_serialize:
            b[attr_name] = getattr(self, attr_name)
        return b

DEFAULT_METHODS = ['get']
    
class RestEndpoint:

    def __init__(self):
        self.methods = {}

        for method_name in DEFAULT_METHODS:
            method = getattr(self, method_name.lower(), None)
            if method:
                self.register_method(method_name, method)

    def register_method(self, method_name, method):
        self.methods[method_name.upper()] = method

    #    wanted_args = list(a.signature(method).parameters.keys())
        available_args = a.match_info.copy()
        available_args.update({'a': a})
     #   return await method(**{arg_name: available_args[arg_name] for arg_name in wanted_args})


class CollectionEndpoint(RestEndpoint):
    def __init__(self, resource):
        super().__init__()
        self.resource = resource

    async def get(self) -> a:
        data = []

        news = s.query(News).all()
        for instance in self.resource.collection.values():
            data.append(self.resource.render(instance))
        data = self.resource.encode(data)
        return a ( status=200, body=self.resource.encode({
            'news': [
                {'id': news.id, 'title': news.title, 'date': news.date,
                'body': news.body, 'deleted': news.deleted }

                    for news in s.query(News)

                    ]
            }), content_type='application/json')
        comments = f.query(Comments).all()
        for instance in self.resource.collection.values():
            data.append(self.resource.render(instance))
        data = self.resource.encode(data)
        return a ( status=200, body=self.resource.encode({
            'comments': [
                {'id': comments.id, 'news': comments.news, 'title': comments.title, 'date': comments.date,
                'comment': comments.comment }

                    for comment in f.query(Comments)

                    ]
            }), content_type='application/json')


class InstanceEndpoint(RestEndpoint):
    def __init__(self, resource):
        super().__init__()
        self.resource = resource

    async def get(self, instance_id):
        instance = s.query(News).filter(News.id == instance_id).first()
        if not instance:
            return a(status=404, body=json.dumps({'not found': 404}), content_type='application/json')
        data = self.resource.render_and_encode(instance)
        return a(status=200, body=data, content_type='application/json')
    
        instance = f.query().filter(Comments.id == instance_id).first()
        if not instance:
            return a(status=404, body=json.dumps({'not found': 404}), content_type='application/json')
        data = self.resource.render_and_encode(instance)
        return a(status=200, body=data, content_type='application/json')


    async def delete(self, instance_id):
        news = s.query(News).filter(News.id == instance_id).first()
        if not news:
            print(404, message="Note {} doesn't exist".format(id))
        a.delete(news)
        a.commit()
        return a(status=204)
        comments = f.query(Comments).filter(Comments.id == instance_id).first()
        if not comments:
            print(404, message="Note {} doesn't exist".format(id))
        a.delete(comments)
        a.commit()
        return a(status=204)
    
    
    async def deleted(self, instance_deleted):
        news = s.query(News).filter(News.deleted == instance_deleted).first()
        if not news:
            print(404, message="Note {} doesn't exist".format(bool))
        a.delete(news)
        a.commit()
        return a(status=204)
    
    async def date(self, instance_date): 
        news = s.query(News).filter(News.date == instance_date).first()
        if not news:
            print(404, message="Note {} doesn't exist".format(datetime))
        s.delete(news)
        s.commit()
        return a(status=204)
        comments = f.query(News).filter(News.date == instance_date).first()
        if not commets:
            print(404, message="Note {} doesn't exist".format(datetime))
        f.delete(comments)
        f.commit()
        return a(status=204)
    
from aiohttp.web import Application, run_app
from aiohttp_rest import RestResource
from models import News

news = {}
comments = {}
app = Application()
person_resource = RestResource('news', News, news, ('title', 'date', 'bodyt', 'deleted'),'comments', Comments, comments, ('news','title', 'date', 'comment') )
person_resource.a(app.router)

if __name__ == '__main__':

    run_app(app)
