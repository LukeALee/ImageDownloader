# -*- coding: utf-8 -*-
import scrapy
import os
import sys
import urllib
import codecs
import time
import random
from scrapy.selector import Selector
from imageDownloader.items import ImagedownloaderItem

class DownloaderSpider(scrapy.Spider):
    name = "downloader"
    #allowed_domains = ["baike.baidu.com/"]
    start_urls = ['http://baike.baidu.com/fenlei/%E7%A7%8D%E5%AD%90%E6%A4%8D%E7%89%A9%E9%97%A8','http://baike.baidu.com/fenlei/%E7%99%BE%E5%90%88%E4%BA%9A%E7%BA%B2']    
    def parse(self,response):
        t=random.randint(1,3)
        time.sleep(t)
     #对物种列表，获取链接并调用parse_details
        for each in  response.xpath('//*[@id="content-main"]/div[3]/div[2]/div[1]/div/div/ul/li'):
            url=each.xpath('./div[1]/a/@href').extract_first()
            if url is not None:
                yield scrapy.Request("http://baike.baidu.com"+url,callback=self.parse_details, dont_filter=True)
        #print 'THINGS GETTING DONE'
        #对于下一页，递归调用自身
        for next_page in response.xpath('//*[@id="pageIndex"]/a/@href').extract():
            print("*********************Nextpage**********************")
            if next_page is not None:
                yield scrapy.Request("http://baike.baidu.com/fenlei/"+next_page,callback=self.parse_item,dont_filter=True)
        #print 'NEXTPAGE NEXTPAGE NEXTPAGE NEXTPAGE NEXTPAGE'
        #对于下级/相关分类，获取链接列表，对列表内每一个链接递归调用自身
        for next_page in response.xpath('//*[@id="content-main"]/div[3]/div[2]/div[1]/div[2]/div[1]/a/@href').extract():
            print(">>>>>>>>>>>>>>>>>>>>Under class<<<<<<<<<<<<<<<<<<<<")
            if next_page is not None:
                yield scrapy.Request("http://baike.baidu.com"+next_page,callback=self.parse,dont_filter=True)
        #print 'RELATION RELATION RELATION RELATION RELATION'
        #for next_page in response.xpath('//*[@id="content-main"]/div[3]/div[2]/div[1]/div[2]/div/div/a/@href').extract():
        #    print("相关分类")
        #    if next_page is not None:
        #        yield scrapy.Request("http://baike.baidu.com"+next_page,callback=self.parse,dont_filter=True)
    def parse_item(self,response):
     #对物种列表，获取链接并调用parse_details
        for each in  response.xpath('//*[@id="content-main"]/div[3]/div[2]/div[1]/div/div/ul/li'):
            url=each.xpath('./div[1]/a/@href').extract_first()
            if url is not None:
                yield scrapy.Request("http://baike.baidu.com"+url,callback=self.parse_details, dont_filter=True)
    def parse_details(self,response):
        t=random.randint(1,3)
        time.sleep(t)
        item=ImagedownloaderItem()
        kind=response.xpath('//div[@class="main-content"]/dl/dd/h1/text()').extract_first()
        dir_name=u"%s"%kind
	item['kind']=dir_name
	path=os.path.join("C:\pics",dir_name)
        if not os.path.exists(path):
            os.mkdir(path)
	describe=""
	for each in response.xpath('//div[@class="lemma-summary"]/div/text()').extract():
	    describe+=u"%s"%each
	txt_name=u"%s.txt"%dir_name
        print(txt_name)
        os.chdir(path)
	with codecs.open(txt_name,'w','utf-8') as f:
	    f.write(describe)
        print "*describe writed*\n"
	pic_album_url=response.xpath('//div[@class="summary-pic"]/a/@href').extract_first()
	if pic_album_url is not None:
	    yield scrapy.Request("http://baike.baidu.com"+pic_album_url,meta={'item':item},callback=self.parse_pic,dont_filter=True)
	else:
            print "pic_album_url not find"
        #imageurl=response.xpath('//div[@class="summary-pic"]/a/img/@src').extract_first()
        #file_name=u"%s.jpg"%imageName
        #path=os.path.join("D:\pics",file_name)#拼接这个图片的路径，放在D盘的pics文件夹下
        #item['imagename']=file_name 
        #item['imageurl']=imageurl
        #print item["imagename"],item["imageurl"]  
        #yield item  #返回item,这时会自动解析item  
        #urllib.urlretrieve(imageurl,path)  #接收文件路径和需要保存的路径，会自动去文件路径下载并保存到我们指定的本地路径
    def parse_pic(self,response):
        print "\nALBUM ADDRESS GETTING\n"
        item = response.meta['item']
        pic_album_find=response.xpath('//div[@class="album-info"]/span[@class="album-text"]/a[@class="album-name"]/@href').extract_first()
        if pic_album_find is not None:
                yield scrapy.Request("http://baike.baidu.com"+pic_album_find,meta={'item':item},callback=self.parse_album_list,dont_filter=True)
        else:
                print "pic_album_find not find"
        t=random.randint(1,3)
        time.sleep(t)
    def parse_album_list(self,response):
        print "\nBEGINING GET ALBUM LIST\n"
        item = response.meta['item']
        for each in response.xpath('//div[@class="pic-list"]/a'):
            url=each.xpath('./@href').extract_first()
            print item['kind']
            if url is not None:
                print "\nDOWNLOADING WILL BEGIN FROM:%s\n"%url
                yield scrapy.Request("http://baike.baidu.com"+url,meta={'item':item},callback=self.parse_save, dont_filter=True)
        print '\n%s: DOWNLOADING END\n'%item['kind']
        t=random.randint(1,3)
        time.sleep(t)
    def parse_save(self, response):
        print "\nDownloading start\n"
        item = response.meta['item']
        image_url=response.xpath('//*[@id="imgPicture"]/@src').extract_first()
        item['imageurl']=image_url
        file_name=image_url.split('/')[-1]
        item['imagename']=file_name
        folder=item['kind']
        path=os.path.join("C:\pics",folder,file_name)
        print "image_url:%s"%image_url
        print "folder%s"%folder
        print "filename:%s"%file_name
        print "path:%s\n"%path
        yield item
        urllib.urlretrieve(image_url,path)
	t=random.randint(1,3)
        time.sleep(t)	

