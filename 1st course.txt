#To find out no's of times a user clicks a perticular URL
def record_user_click(index,keyword,url):
	urls=lookup(index,keyword)
	if urls:
		for entry in urls:
			if entry[0]==url:
				entry[1] = entry[1]+1


def add_to_index(index,keyword,url):
	for entry in index:
		if entry[0]==keyword:
			for element in entry[1]:
				if element[0]=url:
					return 
			element[1].append([url,0])
			return
	index.append([keyword,[[url,0]]])



def get_next_target(page):
	start_link = page.find('<a href=')
	if start_link == -1:
		return None , 0
	start_quote = page.find('"',start_link)
	end_quote = page.find('"',start_quote+1)
	url = page[start_quote+1:end_quote]
	return url,end_quote

def get_all_links(page):
	links=[]
	while True :
		url, endpos = get_next_target(page)
		if url:
			links.append(url)
			page=page[endpos:]
		else:
			break
	return links



#Crawling a web 
def crawl_web(seed): #This method is used to crawl through different pages using a seed page
    tocrawl = [seed]
    crawled = []
    index = []
    while tocrawl:
        page = tocrawl.pop()  #.pop() is used to accesses and remove the element from the list 
        if page not in crawled:
            content = get_page(page)
            add_page_to_index(index, page, content)
            union(tocrawl, get_all_links(content))
            crawled.append(page)
    return index

#Define a procedure Lookup , that takes two inputs: 1. Index 2. Keyword .
#The output should be alist fo URL's associated with the keyword .
#If the keyword is not in the Index,the output should be an empty list.
def lookup(index,keyword):
	for entry in index:
		if entry[0]==keyword:
			return entry[1]
	return []


#Define a procedure , add_page_to_index , that take three input's 1.Index,2.URL,3.Content
#It should update the index to include all of the word occurences found in the page contect by addding the url to the words associated URL List
def add_page_to_index(index,url,Content):
	words=Content.split()   #string stored in content is slitted and later each word is added to index
	for word in words:
		add_to_index(index,word,url)

index = []
add_page_to_index(index,'https://www.udacity.com/course/cs101.','Udacity course on search engine')
print(lookup(index,'Udacity'))
print(lookup(index,'search'))
add_to_index(index,'karan','https://www.udacity.com/course/cs101')
print(index)

#Define a function, hash_string that takes as inputs a keyword(string) and a no's of buckets, and outputs a number representing the bucket for that keyword.
def hash_string(keyword,buckets):
  h = 0
  for c in keyword:
    h = (h+ord(c))%buckets
  return h

print(hash_string('udacity',12))



#Define a procedure, make_hashtable that takes as input a number , nbuckets and outputs an empty hash table with nbuckets empty buckets.
def make_hashtable(nbuckets):
  table=[]
  for x in range(0,nbuckets):
    table.append([])
  return table
  


#Define a procedure hashtable_get_bucket, that takes two iputs a hashtable,and a keyword and outputs thhe bucket where the keyword could occur.
#Hashtable_get_bucket should find the bucket.
def hashtable_get_bucket(htable,key):
  return htable[hash_string(key,len(htable))]


#Define a procedure, hashtable_add(htable,key,value) that adds the key to the hashtable with the associated value.
def hashtable_add(htable,key,value):
  bucket=hashtable_get_bucket(htable,key)
  bucket.append([key,value])

table = make_hashtable(4)
hashtable_add(table,'udacity',23)  #For actual web index the values will be a list of URL's
hashtable_add(table,'UCL','http.realmadridwin')
hashtable_add(table,'UCL','Yashraj')  
print(table)
print(hashtable_get_bucket(table,'UCL'))

#Define a procedure, hashtable_lookup(htable,key), that table 2 inputs a hashtable and a key and outputs the value associated with that key.
#If output key is not in the table, output None.
def hashtable_lookup(htable,key):
  bucket=hashtable_get_bucket(htable,key)
  for entry in bucket :
    if entry[0]==key:
      return entry[1]
  return None

print(hashtable_lookup(table,'UCL'))

#Define a procedure hashtable_update(htable,key,value),that updates the value associated with the key.If key is already in the table,change the value to new value .Otherwise add a new entry for the key and value.
def hashtable_update(htable,key,value):
  bucket=hashtable_get_bucket(htable,key)
  for entry in bucket:
    if entry[0]==key:
      entry[1]=value
      return
  bucket.append([key,value])

print(hashtable_update(table,'King','14UCL'))

print(hashtable_get_bucket(table,'UCL'))
print(make_hashtable(4))


def split_string(source, splitlist): # This method is used to remove all the symbols that are present in the HTML pages.
    if splitlist == ',':
        return source.split(",") #This part if for the cases where there no other punctuation marks except ','.
    else:
        for item in splitlist:  #And this part is for any punctuation marks that are present between strings.
            for i in range(len(source)):
                find_pos = source.find(item)
                if find_pos != -1:
                    source = source.replace(item, " ")
        return source.split()
