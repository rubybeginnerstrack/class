desc 'create new post'

def slugify(title)
  title.downcase.gsub!(/\s/, '-')
end

task :post, :title do |post, args|
  date = Time.now.strftime("%Y-%m-%d")
  title = args[:title]
  open("_posts/#{date}-#{slugify(title)}.markdown", "w") do |f|
    f.write %Q{---
layout: post
title:  #{title}
date:   #{date}
---}
  end
end