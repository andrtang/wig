# Cache Class Documentation

The 'Cache' class is an extension of Python's 'queue.Queue', which is used to store HTTP requests and responses. It prevents redundant requests by caching and reusing them during a defined time period.

This cache is saved on disk, allowing it to persist across multiple scans. Older  cache files are automatically removed based on the time-to-live of the cache.

## Attributes
|Name|Description|
|----|-----------|
|host|The host name of the target being scanned. Used to identify the cache file.|
|cache_dir|The directory where cache files are stored.|
|cache_name|The unique cache file name for the current scane, which includes the host and the timestamp.|
|cache_ttl|The time-to-live for cache files in seconds.|
|queue|A dictionary holding the cached requests and responses.|

## Methods
|Name|Description|
|----|-----------|
|\_\_getitem__(path)|Retrieves the cached response for a given path.|
|\_\_setitem__(path, response)|Caches a new response for the given path.|
|\_\_contains__(url)|Checks whether a URL exists in the cache.|
|set_location(cache_dir)|Sets or creates the cache directory. The default directory is $HOME/.wig_cache. Falls back to the current folder on error.|
|set_host(host)|Sets the host for the current cache and creates the cache filename.|
|get\_num_urls()|Returns the number of unique URLs stored in cache.|
|get_urls()|Retrieves all cached URLs.|
|get_responses()|Retrieves all cached responses.|
|save()|Saves the current cache to disk. The cached is saved based on the hostname and timestamp.|
|load()|Loads a previously saved cache for the host if it exists and is still valid.|

## Internal Methods
|Name|Description|
|----|-----------|
|\_init(self, maxsize)|Initalizes the cache attributes such as the queue, hsot, and cache_ttl.|
|\_check_or_create_cache()|Ensures that the cache directory exists and creates one if necessary.|
|\_remove_old_caches()|Removes cache files that are older than the TTL.|
|\_get\_name\_for\_cache_file()|Generates a unique name for the cache fole based on the hostname and timestamp.|

## Example and Usage
In the example below, we will do the following:
- **Initalizing** with \_\_init__(self, maxsize), set_location() and set_host()
- **Caching Responses** with \_\_setitem__() and \_\_getitem__().
- **Saving and Loading the Cache** with save() and load()
```
# initalize cache and set the location and host
cache = Cache(maxsize=1000)
cache.set_location(None)  
cache.set_host('example.com')

cache.load() # load from the disk if it exists

url = 'http://example.com/resource'

# check if the response is already cached
if url in cache:
    print("Cache hit: Loading response from cache")
    response = cache[url] # calls cache.__getitem__(url, response)
else:
    print("Cache miss: Fetching and caching response")
    
    
    # example response
    response = {
        'status_code': 200,
        'content': '<html>...</html>'
    }
    
    cache[url] = response # calls cache.__setitem__(url, response)

cache.save() # persist the cache to the disk

# output the cached responses
print("Cached URLs:", cache.get_urls())
print("Cached Responses:", cache.get_responses())
```

