# VSTS service account nuget.config location

When a VSTS build agent is installed in a computer it will have a different location for your nuget.config file.
Usually the file is located in `%APPDATA%\NuGet\NuGet.Config` but when on a service account it is located at 
`C:\Windows\ServiceProfiles\NetworkService\AppData\Roaming\NuGet\Nuget.config`.
