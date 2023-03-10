# service-go-hello-api
This is the service implementation for the api for hello world
## Features
* Implementation via [Cobra](https://github.com/spf13/cobra)
* Configuration via [Viper](https://github.com/spf13/viper)
* Error handling via [the Catalyst Platform Utils library](https://github.com/catalystsquad/app-utils-go) which includes support for sentry
* Utilities from catalyst squad such as logging via logrus
* Command configuration validation via [Go validator](https://github.com/asaskevich/govalidator)
* Basic skaffold.yaml included
* Basic _non-root_ Dockerfile included
* Github workflows for release and image uploading included

## FAQ
### How do I add a new command?
You can do it manually by adding a new .go file in the `cmd` directory, but I'd recommend using the [cobra cli generator](https://github.com/spf13/cobra-cli/blob/main/README.md)

1. Install the generator globally with `go install github.com/spf13/cobra-cli@latest`
2. Run `cobra-cli add myCommand`. This will generate a new go file and the skeleton for your new command.
3. Add your flags if any in the `init()` method. See cmd/example.go for an example
4. Add your config struct with validators
5. Implement your run function. If you have config, make sure you call `GetConfigFromViper(config)` in the run function, it will not work in the `init()` function.
6. See `cmd/example.go` for an example

### How do the flags and viper work together?
Viper's global config is configured in `cmd/root.go#initConfig()`. We're using it to attempt to read configuration files as well as calling `viper.AutomaticEnv()` which will bind environment variables to flags when `viper.BindPFlags(flags)` is called

Binding flags should be done in each command's `init()` function. Calling `viper.BindPFlags(flags)` binds whatever configuration viper has found to viper. That could be a .yaml file, or env vars, or cli flags like --my_flag, this call makes viper aware of the flags and configs.

### Why should I use `_` instead of `-` in my flag names?
Simplicity. If your flag names are all strings separated with `_` then you can simply use an identically named environment variable to set the setting. If you use `-` then there's other viper config and trickery you have to do to get it to read the env vars correctly. We don't believe it's worth the extra stuff, just use `_` and it works well out of the box.

### HALP
If you need more help, create an issue, or reference the more detailed linked documentation to all the components comprising this app

### Running locally

The easiest way to run locally is with skaffold. Have a local cluster of your choice and run:

`skaffold dev --tolerate-failures-until-deadline=true`

If you want to run it locally without that, you just need to have the DATABASE_URI env var set appropriately (see tests for a testing example) and a postgres database running somewhere at that URI

