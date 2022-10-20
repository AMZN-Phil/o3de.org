---
linkTitle: Overview
title: Project Repository Overview
description: Project repositories provide a way to share projects in Open 3D Engine (O3DE).
weight: 100
toc: true
---


Project repositories provide a way to discover, acquire, and share projects that are not included with **Open 3D Engine (O3DE)**. Project repositories are generally hosted online on a webserver or GitHub.


## Add a remote project from a project repository in Project Manager

You can add project repositories from the **Projects** page in Project Manager which you can access from the **New Project...** menu.
1. Select the **Add a remote project** button. In the dialogue box that opens you can supply the URL for a project repository. 
1. Copy and paste the URL into the `Remote URL` field.
1. Once the repository has been validated, select a download location and press the **Download** or **Download & Build** button.

A new entry will be added to the list of remote sources, and the project whill be downloaded and will be available from the **Projects** page in Project Manager.

## Add a remote project with the o3de command line tool

You can use the `o3de` CLI tool to add project repositories with the following command:
```
scripts\o3de.bat register -ru <Project repository URL>
```
Once the project repository has been added as a remote source then a project can be downloaded with the following command:
```
scripts\o3de.bat download -p <Project name>
```

## Anatomy of a project repository  

A project repository contains a `repo.json` file which contains the metadata for the repository, including a list of URIs for the projects it provides.

### Folder structure

The recommended folder structure is to put the `repo.json` file at the root of your project repository and the `project.json` files for each project it provides in subfolders.  
```
/
   repo.json
   ExampleProject1/
       project.json
   ExampleProject2/
       project.json
```

If you are confident you will only ever have one project in the repository, it's acceptable to put the `project.json` in the root folder.

{{< tip >}}
One of the most convenient ways to host a project repository online is to place the `repo.json` file inside the GitHub repository where your project's source code is.
{{< /tip >}}

### Format of a repo.json file
The `repo.json` file contains the metadata for the project repository and a list of projects.

```json
{
    "repo_name":"RepositoryName",
    "origin":"RemoteOriginName",
    "repo_uri": "https://o3de.org/repo/v1",
    "summary": "Example Repository",
    "additional_info": "Additional information about your repository including HTML links to any relative website, documentation or licenses.",
    "last_updated": "10/26/2021 04:56pm",
    "projects": [
        "https://o3de.org/repo/v1/ExampleProject/"
    ]
}
```

### Format of project.json files 

The `project.json` file for your projects will need to be modified to include the `origin_uri`, `repo_uri`, `last_updated`, and `sha256` fields.
```json
{
    "project_name": "DownloadableProject",
    "display_name": "Downloadable Project",
    "license": "Apache-2.0 Or MIT",
    "origin": "Test Creator",
    "origin_uri": "https://o3de.org/repo/v1/TestProject/TestProjectV1.zip",
    "repo_uri": "https://o3de.org/repo/v1", 
    "last_updated": "2021-11-18",
    "sha256":"",
    "type": "Tool",
    "summary": "The Downloadable Project provides an example of a downloadable project.",
    "canonical_tags": [
        "Project"
    ],
    "user_tags": [
        "DownloadableProject"
    ],
    "icon_path": "preview.png",
    "requirements": "",
    "documentation_url": "https://o3de.org/docs",
    "dependencies": []
}
```
| Field | Description |
| --- | --- |
| origin_uri | The URI for the `.zip` file containing the project archive.  This is a direct link to download the project's `.zip` archive. |
| repo_uri | The URI for the project repository. |
| last_updated | The date this file or project was last updated in `YYYY-MM-DD`, `YYYY-MM-DD HH:MM:SS`, or `YYYY-MM-DDTHH:MM:SS` format. |
| sha256 | The SHA-256 digest of the `.zip` archive that the `origin_uri` field points to.  You can omit this field for testing, but we highly recommend including it. |

{{< note >}}
For a full description of `project.json` fields and examples see the [`project.json` manifest documentation](/docs/user-guide/project-config/).
{{< /note >}}

### Add a project to the project repository
1.  Make a folder inside your project repository for the project.  In this example, the project is named `RemoteExample`. Make a folder named `RemoteExampleV1` and create a `project.json` file in the folder that looks like this:
    ```json
    {
        "project_name": "RemoteExample",
        "display_name": "Example Remote Project",
        "license": "Apache-2.0 Or MIT",
        "origin":"RemoteOriginName",
        "origin_uri": "http://localhost:8080/RemoteExampleV1/RemoteExampleV1.zip",
        "sha256":"",
        "type": "Code",
        "summary": "An example remote project.",
        "canonical_tags": [
            "Project"
        ],
        "user_tags": [
            "RemoteExample"
        ],
        "icon_path": "preview.png",
        "requirements": "",
        "documentation_url": "https://o3de.org/docs",
        "dependencies": []
    }
    ```

    The `origin_uri` points to the location of the project's `.zip` archive that will be downloaded.

{{< tip >}}
To sync a GitHub repository, the repository should contain a single project. Both the ``repo_uri`` field in ``repo.json`` and the ``origin_uri`` and ``projects`` fields in ``project.json`` should be set to use the GitHub web URL including .git, for example, ``https://github.com/O3DE/RemoteExample.git``
{{< /tip >}}

1. Create a project, or take an existing project, zip up the project's contents, not the folder itself, and place the `.zip` in the "RemoteExampleV1" folder, and name it `RemoteExampleV1.zip`.
{{< tip >}}
When creating an archive of your project, zip up the contents of the project folder, not the folder itself, so that when extracted, the `project.json` file will be at the root, and not inside a subfolder named `RemoteExampleV1` 
{{< /tip >}}
1. Generate the SHA-256 for your project's `.zip` archive and add that value to the `sha256` field of the `project.json` file located outside the `.zip`.  On Windows, you can use the `certutil` program to generate the SHA-256 value like this:
    ```
    certutil -hashfile C:/path/to/project.zip SHA256
    ```
    {{< note >}}
It is possible, but not recommended, to leave out the SHA-256 field for testing.
{{< /note >}}
1. Update your `repo.json` file and add the URL for the project folder.  This example uses the localhost URL for testing.
    ```json
    {
        ...
        "projects": [
            "http://localhost:8080/RemoteExampleV1/"
        ]
    }
    ```


### Testing your local project repository

To test your project repository before you upload it to a server, you can host a local server using Python or register the folder path to your project repository. If you register the folder path, you'll need to modify the URIs to use absolute paths like `file://c/path/to/project` instead of `http://localhost:8080/project`.

1. Use Python to host a local server for testing.
    ```bash
    cd path/to/project/repository 
    
    # start a local python server on 8080
    python -m http.server 8080
    ```
1. Open up Project Manager and add your project repository.  When prompted, use the URL for your local server, which should be `http://localhost:8080/`.
{{< note >}}
Adding the project repository may fail if you leave out the trailing slash in the URL.
{{< /note >}}