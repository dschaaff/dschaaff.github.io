---
author: dschaaff
categories:
- techology
comments: true
date: "2016-11-30T19:28:38Z"
link: http://danielschaaff.com/2016/11/30/adventures-in-ruby/
slug: adventures-in-ruby
tags:
- learning
- puppet
- ruby
title: Adventures in Ruby
wordpress_id: 577
---

I'm learning ruby. Finding time to work towards this goal is proving difficult but I'm forcing myself to use ruby wherever possible to aid in my learning. I'll be putting some of my lame code on here to chronicle my learning and _hopefully_ get some feedback on how I can improve things. I recently came across a good opportunity when I needed to generate a list of nodes to use with the [puppet catalog preview tool](https://github.com/puppetlabs/puppetlabs-catalog_preview)

I wanted to get a full picture of my infrastructure and represent all our nodes in the report output without having to manually type a large node list. Puppet already has all my node names so I just need to extract them . My first step was to query the nodes endpoint in puppetdb for all nodes and pipe it into a file.

```bash
curl http://puppetdb.example.com:8080/v3/nodes/ > nodesout.txt
```

The output of this is json with an array of hashes.

```ruby
[{
"name" : "server2.example.com",
"deactivated" : null,
"catalog_timestamp" : "2016-11-28T19:28:14.828Z",
"facts_timestamp" : "2016-11-28T19:28:12.112Z",
"report_timestamp" : "2016-11-28T19:28:13.443Z"
},{
"name" : "server.example.com",
"deactivated" : null,
"catalog_timestamp" : "2016-11-28T19:28:14.828Z",
"facts_timestamp" : "2016-11-28T19:28:12.112Z",
"report_timestamp" : "2016-11-28T19:28:13.443Z"
}]
```

I only want the name of each node so I need to parse that out. It was a great opportunity to open pry and get some practice!





  * load json so I can parse the file



```ruby
[1] pry(main)> require 'json'
=> true
```



  * read in the file



```ruby
[2] pry(main)> file = File.read('nodesout.txt')
```



  * parse the file into a variable



```ruby
pry(main)> data_hash = JSON.parse(file)
=> [{"name"=>"server.example.com",
"deactivated"=>nil,
"catalog_timestamp"=>"2016-11-29T00:37:03.202Z",
"facts_timestamp"=>"2016-11-29T00:37:00.972Z",
"report_timestamp"=>"2016-11-29T00:36:38.679Z"},
{"name"=>"server2.example.com",
"deactivated"=>nil,
"catalog_timestamp"=>"2016-11-29T00:37:03.202Z",
"facts_timestamp"=>"2016-11-29T00:37:00.972Z",
"report_timestamp"=>"2016-11-29T00:36:38.679Z"}]
[4] pry(main)> data_hash.class
=> Array
```



  * setup a method to iterate over the data and write each hostname to a new line in a file



```ruby
[5] pry(main)> def list_nodes(input)
[5] pry(main)*   File.open('nodes_out.txt', 'a') do |f|
[5] pry(main)*     input.each do |i|
[5] pry(main)*       f.puts i["name"]
[5] pry(main)*     end
[5] pry(main)*   end
[5] pry(main)* end
=> :list_nodes
```



  * run the method against my data_hash



```ruby
[6] pry(main)> list_nodes(data_hash)
[7] pry(main)> exit
```

I now have the list of nodes I was looking for!

```ruby
$ cat nodes_out.txt
server.example.com
server2.example.com
```

This accomplished what I needed and also saved me a lot of time (like putting the puppetdb query directly in the ruby stuff). I'm certain there may be a cleaner way to do this, but that's what learning is for!
