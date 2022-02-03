# heroku-buildpack-subdir-to-root-extra-dirs

> This is a heroku buildpack based on https://github.com/techgaun/heroku-buildpack-subdir-to-root.git It does two things, first, it moves a subdirectory to the root of the project. Then, it copies any defined extra_directories to the root of the project too.

I had to modify the original buildbpack because of my project structure. If you have a structure like this
```
-common_sources
-electron_app
-web_app
```

In order for heroku to compile you need to copy the contents of common_sources into the temp folder. Otherwise heroku wont find them and the build will fail.

This buildpack is supposed to be used as the first buildpack
on your workspace containing multiple projects.

### Usage

- Add `heroku-buildpack-subdir-to-root-extra-dirs` as first buildpack

```
heroku buildpacks:add --index 1 https://github.com/GMolini/heroku-buildpack-subdir-to-root-extra-dirs.git --app <YOUR_APP>
```

- Configure `PROJECT_RELATIVE_PATH`

```
heroku config:set PROJECT_RELATIVE_PATH=web_app --app <YOUR_APP>
```

- Configure `EXTRA_DIR_X`. X being 0, 1, 2, etc.
```
heroku config:set EXTRA_DIR_0=common_sources --app <YOUR_APP>
```

In order for the build to work in both development and production, I recommend setting an alias in the webpack config
```
const path = require('path');
let common_src_path = process.env.NODE_ENV === 'development' ? path.resolve(__dirname, '..', 'common_sources') : path.resolve(__dirname, 'common_sources');
module.exports = {
  configureWebpack: {
    resolve: {
      alias: {
        "@common_src": common_src_path,
      },
      extensions: ['.js', '.vue']
    },
    devtool: 'source-map'
  },
}
```

Then you can just import as usual in your sources
```
import main from '@common_src/main.js'
```

- Configure rest of your buildpacks

- Enjoy the working deployment

- If something does not work, feel free to create an issue
