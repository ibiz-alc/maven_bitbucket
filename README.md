## [Android] Publish library in private repository (bitbucket)
This document is recording the story about my progress of modulizing the android library.

1. At first, please regist a bitbucket account and create the repository you want to use.
2. connect with your local repository.

  > **_Note: You need use ssh to authenticate your bitbucket._**

  > If you don't know how to setup ssh, please see [here](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html).

3. One nice blogger wrote the .gradle file to pull all things together.
   Please add the following codes to your project.gradle (import another .gradle file):

    ```
    apply from: 'https://raw.githubusercontent.com/JeroenMols/GitAsMaven/master/publish-bitbucket.gradle'
    ```

   > **_Note: You can see this .gradle file in your browser by the url._**

4. Create a gradle.properties file within your library folder with the following parameters:

    ```
    ARTIFACT_VERSION=<version_here>
    ARTIFACT_NAME=<libraryname_here>
    ARTIFACT_PACKAGE=<packagename_here>
    ARTIFACT_PACKAGING=aar //You could also use jar
    COMPANY=<bitbucket_team_company_here> //Username if not part of team
    REPOSITORY_NAME=<bitbucket_reponame_here>
    ```

5. Create a local.properties file in the root of your project with the following parameters:

    ```
    USERNAME=<username_here> // username or email
    PASSWORD=<password_here>
    ```

6. Finally, Run the following command to upload a version to your Maven repository.

    ```
    ./gradlew uploadArchives
    ```

    > **_Note: This command can be run in the git-bash or in the Android Studio's terminal. (recommend git-bash)._**

7. You can add the following code to your project.gradle file:

  ```
  repositories {
    jcenter()
    maven {
      credentials {
          username USERNAME
          password PASSWORD
      }
      authentication {
          basic(BasicAuthentication)
      }
      url "https://api.bitbucket.org/2.0/repositories/{user_name/team_name}}/{repository_name}}/src/{branch_name}}"
    }
  }
  ```

  > Note: You have to provide a username and password as you are connecting to a private repository.
  >> **Important**
  >>
  >> Make sure not to put your username and password directly into the build.gradle file! That would be a major security risk. Instead have a look at this blogpost to learn how to securely provide a username and password.

8. Congrates! You can use the following gradle dependency to import your library.

  ```
  dependencies {
    compile '{user_name/team_name}:{repository_name}:{version}'
    implementation 'ARTIFACT_PACKAGE:ARTIFACT_NAME:ARTIFACT_VERSION'
  }
  ```

___

> **Q&A:**
> 1. If there is any error when you run the gradle, please use the `--info` to debug, like:
>
>   ```
>   ./gradlew --info uploadArchives
>   ./gradlew clean build publishToMavenLocal
>   ```
>
> 2. If you find **wagon-git** always fail to push your library to bitbucket, please go to the temp directory and push your library to bitbucket manually.
>
>> The folder path (Windows): `C:\Users\{username}\AppData\Local\Temp`
>>
>> (The absolute path can be found in the debug log)

### Resources
[GIT AS A SECURE PRIVATE MAVEN REPOSITORY](http://jeroenmols.com/blog/2016/02/05/wagongit/)