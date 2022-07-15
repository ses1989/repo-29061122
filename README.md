# Running the Example

## Prerequisites

* NodeJs 14.x
* .NET Core 3.1 SDK
* .NET Framework 4.8 SDK
* Visual Studio 2019
* Docker for Windows, with Windows Containers enabled

Ensure you have followed the steps listed on the [installation documentation](https://sitecore.github.io/Helix.Examples/install.html).

The Helix examples assume you have some experience with (or at least an understanding of) Docker container-based Sitecore development. For more information, see the [Sitecore Containers Documentation](https://containers.doc.sitecore.com).


## Initialize

Open a PowerShell administrator prompt and run the following command, replacing the `-LicenseXmlPath` with the location of your Sitecore license file.

```ps1
.\init.ps1 -InitEnv -LicenseXmlPath "C:\path\to\license.xml" -AdminPassword "DesiredAdminPassword"
```

This will perform any necessary preparation steps, such as populating the Docker Compose environment (.env) file, configuring certificates, and adding hosts file entries.

If this is your first time using `mkcert` with NodeJs, you will
need to set the `NODE_EXTRA_CA_CERTS` environment variable. This variable
must be set in your user or system environment variables. The `init.ps1`
script will provide instructions on how to do this.
  * Be sure to restart your terminal or VS Code for the environment variable
    to take effect.

## Build the solution and start Sitecore

Next run the following command.

```ps1
.\up.ps1
```

This will download any required Docker images, build the solution and Sitecore runtime images, and then start the containers.

Once complete, you can access the instance with the following.

* Sitecore Content Management: https://xmcloudcm.localhost
* Basic Company site: https://www.basic-company-nextjs.localhost

## Deploy your environment to XM Cloud

* Login to XM Cloud
```
dotnet sitecore cloud login
```

* Create a Project
```
dotnet sitecore cloud project create -n {PROJECT_NAME}
```

* Create an Environment
```
dotnet sitecore cloud environment create --project-id {PROJECT_ID} -n {ENVIRONMENT_NAME}
```

* NOTE THE ENVIRONMENT ID

* Provision and Deploy the Environment with the Starter Kit source code
```
dotnet sitecore cloud deployment create --environment-id {ENVIRONMENT_ID} --upload
```

* Connect to the environment
```
dotnet sitecore cloud environment connect --environment-id {ENVIRONMENT_ID}
```

* Publish the edge content

```
$connectionName = (dotnet sitecore cloud environment info -id {ENVIRONMENT_ID} --json | ConvertFrom-Json).name
dotnet sitecore publish --pt Edge -n $connectionName
```

## Create an Edge Token

Running the following script with the environment id from the previous steps will create an Edge access token and launch the GraphQL Playground so that you can query content.

After publishing, you can also use this key in order to run the JSS site against.

```ps1
.\New-EdgeToken.ps1 -EnvironmentId {ENVIRONMENT_ID}
```

## Rebuild Indexes

After running `.\up.ps1` for the first time, or if you ever run `\docker\clean.ps1`, you will need to [rebuild the search indexes](https://doc.sitecore.com/developers/101/platform-administration-and-architecture/en/rebuild-search-indexes.html).

You should now be able to view the Basic Company site at https://www.basic-company-nextjs.localhost.

## Stop Sitecore

When you're done, stop and remove the containers using the following command.

```
docker-compose down
```
