# Sitemap Visualizer

## Usage
Install the `ruby` dependencies:

    $ sudo gem install bundler
    $ bundle install --path .bundle/gems

Download the content structure [document](https://docs.google.com/spreadsheet/ccc?key=0AqNYkM7nIwD5dHpXWERDT1U3T3NOWUtUSlgzMlZ4NkE&usp=sharing) as a tab-delimited file, by selecting `File` > `Download as` > `Tab-separated values (.tsv, current sheet)` from the Google Drive interface.

Remove the unnecessary header rows from the downloaded content structure document:

    $ sed '2,3d' path/to/downloaded-file.tsv > path/to/new-file.tsv

Generate the `sitemap.json` file:

    $ bundle exec rake generate < path/to/new-file.tsv

Fire up the built-in web server to access the sitemap visualizer:

    $ bundle exec rake server

Open [localhost:5000](http://localhost:5000/) in your browser.