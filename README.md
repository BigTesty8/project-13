# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES
#### Project13
In this project we will introduce dynamic assignments by using include module.
Now you may be wondering, what is the difference between static and dynamic assignments?
Well, from Project 12, you can already tell that static assignments use import Ansible module. The module that enables dynamic assignments is include.
Hence,
import = Static
include = Dynamic
# INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE
- In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.
![](https://github.com/BigTesty8/project-13/assets/137091610/baa419c9-3366-4e1f-ad2f-57db7c698de4)
- Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.

![](https://github.com/BigTesty8/project-13/assets/137091610/bcf1b2c6-5551-42a1-866c-30c3fa0cd7ff)

#### Your GitHub shall have following structure by now.
![](https://github.com/BigTesty8/project-13/assets/137091610/9996265a-f0cf-4e53-bd09-2d3f450e777b)
Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

Your layout should now look like this.

![](https://github.com/BigTesty8/project-13/assets/137091610/04225fd5-de34-4e51-9e9c-c7e2d4f5e10d) 


Now paste the instruction below into the env-vars.yml file.

![](https://github.com/BigTesty8/project-13/assets/137091610/f6606b3f-09eb-4a90-bf4b-42e1aed3cb3c)

#### Notice 3 things to notice here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
- include_role
- include_tasks
- include_vars

In the same version, variants of import were also introduces, such as:

- import_role
- import_tasks

We made use of a special variables { playbook_dir } and { inventory_file }. { playbook_dir } will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. { inventory_file } on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.

We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

# UPDATE SITE.YML WITH DYNAMIC ASSIGNMENTS
Update site.yml with dynamic assignments
Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

site.yml should now look like this.

![](https://github.com/BigTesty8/project-13/assets/137091610/361f5246-8b75-4804-a8e6-a4f597985f58)

## Community Roles
Now it is time to create a role for MySQL database – it should install the MySQL package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

#### Download Mysql Ansible Role
You can browse available community roles [here](https://galaxy.ansible.com/ui/)

We will be using a MySQL role developed by geerlingguy.
- Inside roles directory create your new MySQL role with ansible-galaxy install geerlingguy.mysql and rename the folder to mysql

![](https://github.com/BigTesty8/project-13/assets/137091610/f7fa6b28-9b9c-45f2-b0a6-c886e96cbbfe)

![](https://github.com/BigTesty8/project-13/assets/137091610/929d3d8c-090f-4635-b756-03f7d663679a)

Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.
# LOAD BALANCER ROLES
#### Load Balancer roles
We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

- Nginx
- Apache
With your experience on Ansible so far you can:

Decide if you want to develop your own roles, or find available ones from the community
Update both static-assignment and site.yml files to refer the roles

## Important Hints:

Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.


- Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.

![](https://github.com/BigTesty8/project-13/assets/137091610/a96db213-3d4c-4bab-b921-cfb1de8aaf44)

![](https://github.com/BigTesty8/project-13/assets/137091610/fd0e41d9-a228-462e-9c7e-2337fa19a716)

- Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.


- Declare another variable in both roles load_balancer_is_required and set its value to false as well


#### Update both assignment and site.yml files respectively

loadbalancers.yml file

![](https://github.com/BigTesty8/project-13/assets/137091610/53047d78-2ab9-4e65-8e96-1dbc3e978c8e)

site.yml file

![](https://github.com/BigTesty8/project-13/assets/137091610/56f3abc8-2a56-4384-870c-68005eefe8de)

Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

![](https://github.com/BigTesty8/project-13/assets/137091610/ccc523e4-6a2e-4f02-93d3-dba81bfd73df)

The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.

To test this, you can update inventory for each environment and run Ansible against each environment.

![](https://github.com/BigTesty8/project-13/assets/137091610/0b34244d-6221-434e-a337-26c240b2071d)

![](https://github.com/BigTesty8/project-13/assets/137091610/bd29a14a-5456-4683-ae6f-61b904704137)
