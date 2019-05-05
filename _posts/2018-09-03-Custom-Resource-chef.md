---
layout: post
title:  "Chef Custom resource"

categories:
  - configuration-management

tags:
  - Chef
  - code
  - Ruby
  -


excerpt: Example of a custom chef resource
---

<b>Read data from a file on disk to process<b>

> I know this example is an overkill, this is just to demonstrate

1. Under resources/readthings.rb
```
resource_name :readthings
property :creds, String, required: true, name_property: true
property :username, String, required: true
property :password, String, required: true
property :location, String, required: true


action :update do
  #print "\n-=-=-=-=-==-=-=-=-=-=-=-=\n"
  #print new_resource.location
  #print "\n-=-=-=-=-==-=-=-=-=-=-=-=\n"


  ruby_block 'scan_contents' do
    block do
      def load_existing_value
        ::File.readlines(new_resource.location).each do |line|
          if line.include?('password')
            arr = line.split('=')
           pre_existing_value = arr[1].tr('"', '')
           existing_value = pre_existing_value.tr(' ','')
           return existing_value
          end
        end
        return existing_value
      end
      existing_value = load_existing_value
      print existing_value
    end
    action :run
  end

end

```

2. In attributes
`default['chef-thing']['cred-path'] = '/automate-credentials.toml'``



3. In recipes
```
readthings "usercreds" do
  location node['chef-thing']['cred-path']
  action :update
end
```
