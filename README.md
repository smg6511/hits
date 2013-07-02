Hits
====

Hits tracks pagehits with a punch. Hits counts MODX Revolution page view and stores them in a custom table. 

With hits you can:
 * record page hits based on a provided hit_key (such as a resource id)
 * Returns the results of a hit query and [use with getResources](#with-getresources)


## Usage
Record a hit for the current resource.

    [[!Hits? &punch=`[[*id]]`]]

Record a hit for resource 3.

    [[!Hits? &punch=`3`]]

Record 20 hit for resource 4

    [[!Hits? &punch=`4` &amount=`20`]]

Remove 4 hit from resource 5.

    [[!Hits? &punch=`5` &amount=`-4`]]

Get a comma-delimited list of ids of the 10 most visited pages 10 levels down from the web context.

    [[!Hits? &parents=`0` &depth=`10` &limit=`10` &outputSeparator=`,`]]

Get a comma-delimited list of ids of the 4 least visited pages that are children of resource 2 and use your own hitInfo chunk to render results.

    [[!Hits? &parents=`2` limit=`4` &dir=`ASC`  &chunk=`hitInfo` &outputSeparator=`,`]]
    
Get the four most hit resources, discluding the first

    	[[!Hits? &parents=`0` &limit=`4` &offset=`1` &outputSeparator=`,`]]
        
Knockout resource 3 then add 2 hits (knockout zeros value before adding punches)

    	[[!Hits? &punch=`3` &amount=`2` &knockout=`1`]]
    

## Available Properties
| Name            | Description                                                                                                                                                                      | Default Value   | Added in version
| ----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| -----:| -------:|
| punch           | If set, a hit_key to record one or more hits for. Usually a resource id.                                                                                                         |                 | 1.0.0
| amount          | The amount of hits to record for the punched hit_key.                                                                                                                            | 1               | 1.0.0
| parents         | Comma-delimited list of ids serving as parents to search for most visited resources within. If provided, results are returned.                                                   |                 | 1.0.0
| depth           | Integer value indicating depth to search for resources from each parent. First level of resources beneath parent is depth.                                                       | 10              | 1.0.0
| tpl             | hit_key, hit_count, and id (of hit) paramters will be passed into the provided chunk for each result.                                                                            | outputs hit_key | 1.0.0
| limit           | The amount of results to return.                                                                                                                                                 | 5               | 1.0.0
| sort            | Property to sort results on. Available options are hit_count, hit_key or id.                                                                                                     | hit_count       | 1.0.0
| dir             | Direction to sort properties on.                                                                                                                                                 |DESC             | 1.0.0
| outputSeparator | An optional string to separate each tpl instance.                                                                                                                                |"\n"             | 1.0.0
| toPlaceholder   | If set, will assign the result to this placeholder instead of outputting it directly.                                                                                            |                 | 1.0.0
| offset          | Optionally offset results                                                                                                                                                        |                 | 1.1.0
| knockout        | Set to 1 to zero hit_count of punched record before incrementing by amount                                                                                                  |                 | 1.2.0                                                       |                 | 1.1.0

## With getResources
Hits can be used be used with [getResources](http://rtfm.modx.com/display/ADDON/getResources) to list the most or least visited pages. This will pass a comma seperated list of ids of the 10 most visited pages according to Hits into getResources.

    [[getResources?
    &resources=`[[!Hits? &parents=`0` &depth=`10` &limit=`10` &outSeperator=`,`]]`
    ...
    ]]
    
## Optimization

#### Recording Hits
Hits needs to be called uncached whenever it is punching hits. If you don't want the processing of a hit to affect page load time you can record your hits after page load using AJAX.

#### Displaying Statistics
When using with getResources remember that you can utilize [getCache](https://github.com/opengeek/getCache/wiki) to cache the results to the filesystem for a determined period time as well as share the cache across multiple pages. If you are displaying a "Most Visited Pages" nav in your sidebar, the results are probably going to be the same across all or multiple pages. Thus, you can utilize the getCache cacheElementKey paramater to share the cache file across multiple (in this case all) resources. Move getResources tag to a 'getMostViewed' Chunk. 

    [[!getCache?
    &element=`getMostViewed`
    &cacheExpires=`900`
    &cacheKey=`hits`
    &cacheElementKey=`getMostViewed`
    ]]
    
The getMostViewed chunk will now only be processed every 15 minutes and load from a single shared cache. This means no matter how many visitors we have, we are only processing this output once every 15 minutes.

Alternatively, if results from getResources vary from page to page, you could wrap the Hits tag with getCache. 

    [[getResources?
    &resources=`[[!getCache? &element=`mostHitsIDs` &cacheExpires=`900` &cacheKey=`hits` &cacheElementKey=`mostHitsIDs`]]`
    ...
    ]]




