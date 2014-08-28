# backup-database-yml-imagemagick-buildpack

First: Why?

https://github.com/kovyrin/db-charmer/issues/54

Sure, we could hack up heroku-buildpack-ruby like this:

https://github.com/vkurnavenkov/heroku-buildpack-ruby/commit/33603c356650df87cb8ed95e41663cb72177e1e5

But that's just silly. Honestly, heroku-buildpack-ruby really shouldn't be overwriting a database.yml file if it exists.

This buildpack makes a copy of your project's deployed /app/config/database.yml into a file that is referenced by the environment `BACKUP_DATABASE_YML`

Then you can add this glorious hack to your `config/initializers/database_initializer.rb`:

```
ActiveRecord::Base.configurations = Rails.application.config.database_configuration = YAML::load(ERB.new(IO.read(ENV['BACKUP_DATABASE_YML'])).result) if File.exists?(ENV['BACKUP_DATABASE_YML'])
```

"What? I don't even?" Agreed.

As this is a layered buildpack, this really needs to be listed _somewhere_ before your final heroku-buildpack-ruby URL in your .buildpacks file, and used with `BUILDPACK_URL` pointed at heroku-buildpack-ruby.
