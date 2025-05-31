# Heroku Buildpack: Eclipse Temurin JRE 21

This is a Heroku buildpack for Java applications that installs Eclipse Temurin JRE 21 with HotSpot JVM.

## Features

- Installs Eclipse Temurin JRE 21 (latest patch version: 21.0.5+11)
- Supports both x64 and ARM64 architectures
- Caches JRE downloads for faster subsequent builds
- Automatically configures Java environment variables
- Enables container support for optimal memory usage in Heroku dynos
- Compatible with Heroku stacks: heroku-20, heroku-22, and heroku-24

## Usage

### Add the buildpack to your Heroku app

```bash
heroku buildpacks:set https://github.com/your-username/heroku-buildpack-temurin-jre21.git
```

Or add it to your `app.json`:

```json
{
  "buildpacks": [
    {
      "url": "https://github.com/your-username/heroku-buildpack-temurin-jre21.git"
    }
  ]
}
```

### For multi-buildpack scenarios

If you're using multiple buildpacks (e.g., with Maven or Gradle), add this buildpack after your build tool:

```bash
heroku buildpacks:add --index 1 heroku/maven
heroku buildpacks:add --index 2 https://github.com/your-username/heroku-buildpack-temurin-jre21.git
```

## Detection

The buildpack will automatically detect Java applications by looking for:

- `pom.xml` (Maven projects)
- `build.gradle` or `build.gradle.kts` (Gradle projects)
- `settings.gradle` or `settings.gradle.kts` (Gradle projects)
- `*.jar` files
- `system.properties` file

## Configuration

### Specifying Java version

Create a `system.properties` file in your project root:

```properties
java.runtime.version=21
```

### JVM Options

The buildpack automatically sets:
- `-XX:+UseContainerSupport` for container-aware memory management

You can add additional JVM options through the `JAVA_TOOL_OPTIONS` environment variable:

```bash
heroku config:set JAVA_TOOL_OPTIONS="-Xmx512m -Xms512m"
```

### Procfile

If no `Procfile` is present, the buildpack will create a default one that runs your JAR file:

```
web: java -jar target/your-app.jar
```

For custom commands, create your own `Procfile`:

```
web: java -cp target/classes:target/dependency/* com.example.MainClass
```

## Environment Variables

The buildpack sets the following environment variables:

- `JAVA_HOME`: Points to the JRE installation
- `PATH`: Updated to include Java binaries

## Project Structure

```
.
├── bin/
│   ├── compile     # Main buildpack script
│   ├── detect      # Detection script
│   └── release     # Release configuration script
├── buildpack.toml  # Buildpack metadata
├── LICENSE         # License file
└── README.md       # This file
```

## Updates and Maintenance

To update the JRE version:

1. Modify the version variables in `bin/compile`:
   - `JRE_FULL_VERSION`: Full version string (e.g., "21.0.5+11")
   - `JRE_URL_VERSION`: URL-formatted version (e.g., "21.0.5_11")

2. Update the version in `buildpack.toml`

3. Test the buildpack with your applications

## Troubleshooting

### Build fails with "Failed to download JRE"

- Check if the Temurin version is available at [Adoptium API](https://api.adoptium.net/v3/info/available_releases)
- Verify the download URL format matches the current API

### Java version mismatch

- Ensure your application is compatible with Java 21
- Check for any hardcoded Java version requirements in your build files

### Memory issues

- Heroku dynos have limited memory; ensure your JVM settings are appropriate
- The `-XX:+UseContainerSupport` flag helps Java respect container memory limits

## License

MIT License - see LICENSE file for details

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Support

For issues and questions:
- Open an issue on GitHub
- Check Heroku's Java documentation
- Visit Eclipse Temurin documentation at https://adoptium.net/
