# -*- mode: ruby -*-
# vi: set ft=ruby ts=2 sw=2 :

require "active_support"
require "csv"
require "json"

class Node
  attr_accessor :name, :title, :type, :level, :children

  def initialize(title, type = nil)
    @name = normalize(title)
    @title = title
    @type = !type.nil? ? type : "undef"
    @level = 0
    @children = []
  end

  def to_json(*a)
    { "name" => name, "title" => title, "type" => type, "children" => children }.to_json(*a)
  end
end

def normalize(str)
  str = ActiveSupport::Multibyte::Chars.new(str)
  str.normalize(:kd).gsub(/[^x00-\x7F\x20]/n, '').gsub(/[\s]+/, '-').downcase.to_s
end

def build_tree(rows)
  tree = nil
  nodes = []

  rows.each do |row|
    node = Node.new(row["name"])
    nodes.push node

    node.children << Node.new("Artículos Hub", "landing") if row["articles"].match(/yes/i)
    node.children << Node.new("Galerías Hub", "landing") if row["galleries"].match(/yes/i)
    node.children << Node.new("Videos Hub", "landing") if row["videos"].match(/yes/i)
    node.children << Node.new("Rankits Hub", "landing") if row["rankits"].match(/yes/i)
    node.children << Node.new("Versus Hub", "landing") if row["versus"].match(/yes/i)
    node.children << Node.new("Resúmenes Hub", "landing") if row["recaps"].match(/yes/i)
    node.children << Node.new("Capítulos Hub", "landing") if row["episodes"].match(/yes/i)
    node.children << Node.new("Personajes Hub", "landing") if row["characters"].match(/yes/i)
    node.children << Node.new("Novelas Clásicas", "landing") if row["clasicas"].match(/yes/i)
    node.children << Node.new("Talento Hub", "landing") if row["talent"].match(/yes/i)
    node.children << Node.new("Jueces Hub", "landing") if row["judges"].match(/yes/i)
    node.children << Node.new("Concursantes Hub", "landing") if row["contestants"].match(/yes/i)
    node.children << Node.new("Presentadores Hub", "landing") if row["hosts"].match(/yes/i)

    parent_name = normalize(row["parent"])
    parent_node = nodes.find { |node| node.name == parent_name }
    if parent_node
      node.level = parent_node.level + 1
      node.type = node.level == 1 ? "superhub" : "hub"
      parent_node.children << node
    else
      node.type = "root"
      tree = node
    end
  end

  tree
end

task :generate do |t, args|
  rows = []
  input = STDIN.read unless STDIN.tty?
  if !input.nil?
    CSV.parse(input, {:headers => true, :converters => :all, :col_sep => "\t"}) do |row|
      rows.push(row) unless !(row["create"] =~ /yes/i) || row["name"] == "-"
    end
  end

  tree = build_tree(rows)
  File.open("web/sitemap.json", "w") do |file|
    file.write(JSON.pretty_generate(tree)) unless tree.nil?
  end
end

task :server, :port do |t, args|
  args.with_defaults(:port => "5000")

  Kernel.system("ruby -run -e httpd web/ -p #{args[:port]}")
end

