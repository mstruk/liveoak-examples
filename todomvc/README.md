# LiveOak AngularJS TodoMVC Example
===================================
This example is based on the example http://todomvc.com/architecture-examples/angularjs/#/ . Our example is integrated with [Keycloak](http://www.keycloak.org) and it's showing security possibilities of [Liveoak](http://liveoak.io) (among other things).

Installing the application
--------------------------

Assumption is that:
* $LIVEOAK points to directory with your Liveoak server
* $LIVEOAK_EXAMPLES points to directory with Liveoak examples

So then copy the example in the _apps_ directory of your Liveoak server and start the server
```shell
$ cp -r $LIVEOAK_EXAMPLES/todomvc $LIVEOAK/apps
$ sh $LIVEOAK/bin/standalone.sh -b 0.0.0.0
````

Setup the application
---------------------

* Create roles for your application (Manual step required)
  * Go to [http://localhost:8080/admin](http://localhost:8080/admin) and login as user "admin" with password "admin"
  * Go to [http://localhost:8080/admin#/applications/todomvc/application-settings](http://localhost:8080/admin#/applications/todomvc/application-settings) and add 2 new roles "admin" and "user". Then you can also select "user" to be default role > Click "Save"
  * Role names are important, because authorization is configured to deal with those 2 roles.

* Add HTML client for newly created application (Manual step currently required)

  * Go to [http://localhost:8080/admin#/applications/todomvc/application-clients](http://localhost:8080/admin#/applications/todomvc/application-clients)
  * Add Client
    * Name: "todomvc-html-client"
    * Platform: HTML-5
    * Redirect URI: "http://localhost:8080/todomvc/*" (click button "Add")
    * Web Origins: "http://localhost:8080" (click button "Add")
    * Scope: select both "admin" and "user" scopes (if you can't add scopes for the first time, let's create client without scopes and then edit it later and add scopes)
   * Finally click "Save"

* Create some default users for testing purposes (their names and default passwords are not important, feel free to use different names). This step is not mandatory as you can automatically register users later on the login screen of the application.
But it's useful as self-registered users always have just default roles (in our case role "user"), so you can't test all the authorization possibilities when all users have just same role "user" .
  * Go to [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users)
  * Add User > username: "bob" > Save
  * Then open [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/user-credentials](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/user-credentials) and fill some initial password for user "bob". Note that bob will need to change this default password when he try to login for the first time.
  * Then open [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/role-mappings](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/role-mappings) and select application "todomvc" and move both available roles "admin" and "user" to the assigned roles. This means that bob will be both "admin" and "user" .
  * Repeat the steps and create another user "john", but assign him just to role "user"
  * Repeat the steps again and create last user "mary" and don't assign her to any role

* Create new collection in MongoDB (Manual step currently required)
  * Go to [http://localhost:8080/admin#/applications/todomvc/storage/storage/browse](http://localhost:8080/admin#/applications/todomvc/storage/storage/browse)
  * Click "New collection" > Fill collection name "todos" (name is important as it's used by the application) > Click "Add"
  * This step is not mandatory because in case that you first login as some admin user (in our case user "bob"), collection will be automatically created during first access to application. But in case that you're using just self-registered users, it will be needed.


Running the application
-----------------------
* Open your browser at [http://localhost:8080/todomvc](http://localhost:8080/todomvc)
* User 'bob' with password 'password' is admin and can do anything. He can create new todos, but he also automatically see todos of all users and he can update or delete them
* User 'mary' with password 'password' doesn't have any roles and she can't do anything. She can't create new todos and also she can't see any todos. Basically mary can't do anything.
You will receive authorization error directly after login as mary.
* User 'john' with password 'password' is normal user. He can create new todos and he can view his own todos and update or delete them, but he can't read, update or delete todos that weren't created by himself.
