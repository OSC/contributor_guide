# The Map of Open OnDemand
This guide attempts to layout structures and locations around much of 
the OOD ecosystem. 

These structures range from types such as github repos, to ruby `gem`s, 
and even to components of OOD itself wrt its codebase.

If you hear a term you don't know, please ask it in Menti!
  - https://osc.github.io/ood-documentation/latest/glossary.html

## Pull Requests
You need to make a fork of the repo first:
- https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo
- Generally you are working off the `latest` branch when you begin your work.
- Edit on GitHub button also works too!

## OOD Documentation
Open OnDemand's documentation sits in a public GitHub repo, hosted out of GH pages.

The repo:
- https://github.com/OSC/ood-documentation
- Notice the repo has many branches but `latest` and `develop` are the 2 which matter 
  because they generate GitHub Pages which serve the documentation for the community.
- Branch off `latest` and push to `develop` for *feautures not implemented*.
- For *typos and fixes*, push your branch back to `latest`.

GH Pages:
- https://osc.github.io/ood-documentation/master/
- https://osc.github.io/ood-documentation/latest/
- https://osc.github.io/ood-documentation/develop/

## Gems
A Ruby `gem` is just a library or module or bundle of code that we can install using the 
ruby package manager `bundler`. 
- The `gem`s are listed in a project's `Gemfile`.
- After a `bundler` run a `Gemfile.lock` with version dependencies will also be generated.
- `bundler`: https://bundler.io/
  - commands: https://bundler.io/docs.html
  - `bundler config set --local path vendor/bundle` will be your friend later to work on 
  the development dashboard code and our local `gem`s without polluting the system `gem`s.

OOD has 4 `gem`s itself, some of which can be largely ignored, some which are quite useful:
- `ood_packaging`: https://rubygems.org/gems/ood_packaging largely for OOD internal team 
  to help with packaging and distribution of OOD.
- `ood_appkit`: https://rubygems.org/gems/ood_appkit
  - Provides an interface to work with OOD scientific apps, a `dataroot` for 
  OOD apps to write data to and common assets and helper objects.
- `ood_support`: https://rubygems.org/gems/ood_support
  - Provides an interface to work with local OS installed on the HPC 
  center's _web node_. This `gem` is often useful for both OOD and OOD apps.
- `ood_core`: https://rubygems.org/gems/ood_core
  - Provides _Adapters_ for _Schedulers_, `batch_connect` _Templates_ for 
  the 3 types of OOD apps, _ACL_ functionality, cluster interactivity, 
  and Job interaction.

## OOD Core (ood_core)
This is where the actual backend code to interact with your clusters or schedulers 
resides. 

### Schedulers and Adapters
OOD provides _adapters_ for the various HPC _schedulers_ which can all be seen here:
- https://github.com/OSC/ood_core/tree/master/lib/ood_core/job/adapters
- One thing to note is we have k8's adapter if you wish to use k8's as a scheduler.
- LinuxHost: This adapter is used to mimic a scheduler or resource manager, for remote desktop or IDE's.
- SystemD: Community contribution.

### 3 Species of OOD App
OOD ships with 3 types of scientific apps:
1. `basic`: https://github.com/OSC/ood_core/blob/master/lib/ood_core/batch_connect/templates/basic.rb
  - HTTP server
  - e.g. Jupyter Notebook: 
    - https://github.com/OSC/bc_osc_jupyter/blob/db927470af05c71edac770ae321a1ff399caec33/submit.yml.erb#L39
2. `vnc`: https://github.com/OSC/ood_core/blob/master/lib/ood_core/batch_connect/templates/vnc.rb
  - vnc server
  - e.g. Qomsol, Remote Desktops
    - https://github.com/OSC/bc_osc_comsol/blob/69667f971076cd2ba2d23bbbdb508bebe20ebc63/submit.yml.erb#L18
3. `vnc_container`: https://github.com/OSC/ood_core/blob/master/lib/ood_core/batch_connect/templates/vnc_container.rb
  - Less common, but it exists, vnc with container for sites that don't want to install X11, XFCE, GNOME, etc. on 
  their host.
All of these options are what you are setting when you select the `template` in your `submit.yml` files.

### Clusters
- The code to work with your clusters and the corresonding cluters config files.
- https://github.com/OSC/ood_core/tree/master/lib/ood_core
  - Split out between 2 files
  - the `clusters.rb` file is to handle the clusters config files.
  - the `clutser.rb` file is to handle working with a cluster and its _scheduler._

## `ood_core` Dev Work
In order for this to work we need to actually touch our `Gemfile` in the `dashboard` and point 
to our local `ood_core`:
```Gemfile
gem 'ood_core', :path=> '/full/path/to/checked/out/ood_core'
```
- You must issue the `bin/setup` command to rebuild your `dashboard` once you make these local changes to your 
`ood_core` code.

### OOD Core PR


## OOD Monorepo
Open OnDemand follows the _Mono-repo_ pattern. What this means is that OOD has many 
various applications utilities all contained under the `Ondemand` namespace.
- https://github.com/OSC/ondemand

Notice we have our `apps` under this which leads to the `dashboard` and all the code 
a user would be familiar interacting with, but at this top level we see much more. 

### `ood-portal-generator`
Generates the NGINX and Apache configs given a valid `ood_portal.yml` file.
### `nginx_stage`
Used to manage the PUN for OOD.
### `ood_auth_map`
Connects the auth system to the web-node system users for OOD at login.
### `packaging`
Used to help with the distribution of OOD software.
### `mod_ood_proxy`
Used to manage the proxy for OOD.
### `dashboard`
This is the frontend `rails` code most users and admins are familiar interacting with.

## OOD Dashboard Code Components
OOD has several components within the `rails` application code
- `active_jobs`
- `bc_desktop`
- `dashboard`
- `file-editor`
- `files`
- `myjobs`
- `projects`
- `shell`
- `system-status`

### MVC Components
OOD uses the MVC pattern of web-development which is default in `rails`.
- https://guides.rubyonrails.org/v7.1/getting_started.html#mvc-and-you
- Rails philosophy is *convention over configuration*.

We can see the OOD conventions by looking at the various `Models`, 
`Controllers` and `Views` within the `rails` code itself.

### Models
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/models
- All the data OOD is aware of is defined in this directory.

### Controllers
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/controllers
- Here we see what data the model can present to a view.

### Views
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/views
- This directory can be daunting as it contains all code used to present the data 
to users. As such, there can be a great many components to any piece of OOD.
  - e.g. https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/views/batch_connect
  - While this is one of the more complex views to deal with, it gives a sense of how complex 
  some of these files can become.
  - The goal for these when working is to try and make things _modular_ and _logical_.
  - Rails also uses the notion of *partials* to provide components of views.
    - These files start with an underscore `_my_partial`.
    - https://osc.github.io/ood-documentation/latest/customizations.html#overriding-pages

### Utilities
OOD also has some helpers for things like `rclone`, some `rake` tasks, and other functionality 
all contained here:
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/lib

### `batch_connect` convention
One of the more powerfule and useful abstractions to be aware of in OOD.
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/models/batch_connect
- This model provides most data you see in a session card and when interacting with forms.
- `helpers` are a big piece of the puzzle here:
  - https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/helpers

## Code Changes
Now that we've had a break neck tour, let's make changes.

This portion assumes you have setup a dev dashboard on your OOD instance as described below:
- https://osc.github.io/ood-documentation/latest/how-tos/app-development/enabling-development-mode.html

And that you have forked, cloned, and built your dashboard as described here:
- https://github.com/OSC/ondemand/blob/master/DEVELOPMENT.md#developing-the-dashboard

### Seeing Changes
Some changes will only require we reload the browser:
- If a config change is not taking effect after several reloads, try a PUN restart.
- **Environment variable changes require a rebuild of the PUN with `bin/setup`**

## Sample PR Walk Through

## GitHub Issue Page
- https://github.com/OSC/ondemand/issues

## Resources:
- Contributing guide: https://github.com/OSC/ondemand/blob/master/CONTRIBUTING.md
- Dev Dashboard Setup: https://github.com/OSC/ondemand/blob/master/DEVELOPMENT.md#developing-the-dashboard
- Development guide: https://github.com/OSC/ondemand/blob/master/DEVELOPMENT.md
- Dockerfile: https://github.com/OSC/ondemand/blob/master/Dockerfile
- Security and Reporting: https://github.com/OSC/ondemand/blob/master/SECURITY.md
- Code of Conduct: https://github.com/OSC/ondemand/blob/master/CODE_OF_CONDUCT.md
- Rails Guides: https://guides.rubyonrails.org/v7.1/
