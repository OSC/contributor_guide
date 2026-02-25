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
- The code to work with your clusters and the corresponding cluters config files.
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
`Controllers` and `Views` within the `rails` code itself. For a 
detailed walkthrough of MVC in OOD, see 
[MVC in the Project Manager](#model-view-controller-in-the-project-manager).

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
One of the more powerful and useful abstractions to be aware of in OOD.
- https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/models/batch_connect
- This model provides most data you see in a session card and when interacting with forms.
- `helpers` are a big piece of the puzzle here:
  - https://github.com/OSC/ondemand/tree/master/apps/dashboard/app/helpers

## Code Changes
Now that we've had a breakneck tour, let's make changes.

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


# Deep Dives

## Model View Controller in the Project Manager

For a practical example of the MVC paradigm in action, we can take a closer look at the Project Manager. 
Although the Project Manager is a single component, it is composed of three different entities: Projects, Launchers, and Workflows.
Each of these entities have their own model, view, and controller, but interact with one another to manage their relationships and data.

### Relationships
The basic relationships necessary for a working project are
- A User has many Projects
- A Project has many Launchers
- A Project has many Workflows
- A Workflow has many Launchers

In a typical web app, these relationships would be defined in a database schema. However the OnDemand dashboard does not use a database, instead managing
its data through the filesystem. So where do these relationships 'live'? The first time these come up is during **routing**. 
```rb
# apps/dashboard/config/routes.rb

Rails.application.routes.draw do
  if Configuration.can_access_projects?
    get 'projects/possible_imports' => 'projects#possible_imports', :as => 'project_possible_imports'
    post 'projects/import' => 'projects#import_save', :as => 'project_import_save'


    resources :projects do
      root 'projects#index'
      get '/jobs/:cluster/:jobid' => 'projects#job_details', :defaults => { :format => 'turbo_stream' }, :as => 'job_details'
      delete '/jobs/:cluster/:jobid' => 'projects#delete_job', :as => 'delete_job'
      post '/jobs/:cluster/:jobid/stop' => 'projects#stop_job', :as => 'stop_job'


      resources :workflows do
        member do
          post 'submit'
          post 'save'
          get 'load'
          get 'clone'
        end
      end


      resources :launchers do
        post 'submit', on: :member
        post 'save', on: :member
        get 'render_button', on: :member
        get 'clone', on: :member
      end
    end
  end
```

At the very top are the routes that are always static for a given user, and thus do not require any parameters to generate their pages. 
For example, 'projects/possible_imports' detects projects that you can access based upon your UNIX group and shared space configurations, and does not have to be connected to an individual project.

Next, we have the line `resources :projects do`, which starts a block that contains the rest of the snippet.
The line is an example of [Rails Resource Routing](https://guides.rubyonrails.org/routing.html#resource-routing-the-rails-default), a shortcut that automatically defines some common routes for a given entity.
Each route defined within this block automatically receives a `/:project/` parameter at the start of their url, meaning they are defined for each project that a user has access to.

Finally, the `resources :workflows do` and `resources :launchers do` lines serve the same function as `resources :projects`, defining basic routes and containing a block of routes that require both a `:project` 
parameter and a `:workflow` or `:launcher` parameter respectively, defining these routes for each workflow or launcher that a project contains.

### Controllers
Each **route** defined above directs the request parameters to a method on a **controller** in order to render that page or perform that action. For some routes, the controller action is explicitly defined while 
others do so implicitly. For example, the line `post '/jobs/:cluster/:jobid/stop' => 'projects#stop_job', :as => 'stop_job'` explicitly points the url to `projects#stop_job`, which Rails interprets as the `stop_job`
method defined on `ProjectsController`.
On the other hand, the line `post 'submit'` does not contain a url or a controller action in the definition. For this route, Rails uses both the `resources :projects do` and `resources :workflows do` blocks containing
the route to generate the url fragment `/:project/:workflow/submit` and direct this to the `submit` method on `WorkflowsController`. 

Following a submit request to WorkflowsController#submit, we see
```rb
# apps/dashboard/app/controllers/workflows_controller.rb

  def submit
    return unless load_project_and_workflow_objects(render_json: true)
    metadata = metadata_params(permit_json_data)
    @workflow.update(metadata)
    submit_param = Workflow.build_submit_params(metadata, project_directory)
    result = @workflow.submit(submit_param)
    if !result.nil?
      render json: { message: I18n.t('dashboard.jobs_workflow_submitted'), job_hash: result }
    else
      msg = I18n.t('dashboard.jobs_workflow_failed', error: @workflow.collect_errors)
      render json: { message: msg }, status: :unprocessable_entity
    end
  end

  private

  def load_project_and_workflow_objects(render_json: false)
    @project = Project.find(project_id)
    @workflow = Workflow.find(workflow_id, project_directory)
    return true if @workflow.present?
    
    if render_json
      render json: { message: I18n.t('dashboard.jobs_workflow_not_found', workflow_id: workflow_id) }, status: :not_found
    else 
      redirect_to project_path(project_id), notice: I18n.t('dashboard.jobs_workflow_not_found', workflow_id: workflow_id)
    end
    false
  end

  def index_params
    params.permit(:project_id).to_h.symbolize_keys
  end

  def project_id
    params.permit(:project_id)[:project_id]
  end

  def workflow_id
    params.require(:id)
  end
```
Notice that **controller actions** are always public methods, and everything under the `private` flag is used within actions, but is not an action itself.
Starting from the top of `submit`, we see the order in which the logic is executed.
- Fetch project and workflow objects based on request parameters
- Fetch metadata from request parameters
- Update the workflow object with metadata
- Create scheduler parameters from workflow object
- Submit scheduler parameters and collect response
- Return a JSON response stating success or failure.

While it is a bit hard to see in the code above, all the parameters included with the request must be accessed through the `params` object, which is available everywhere in the controller.
In this case, since `Workflows#submit` corresponds to an action, not a page, we just send back a JSON response that is rendered by the page the user is currently on (`Workflows#show` in this example).

As we see with `Workflows#submit`, not all controller actions correspond to views. For an example that does render a view at the end, consider the action for `Workflows#show`.
```rb
  def show
    return unless load_project_and_workflow_objects
    launcher_ids = @workflow.launcher_ids

    @launchers = Launcher.all(project_directory).select { |l| launcher_ids.include?(l.id) }
  end
```

Following line-by-line again we see
- Project and workflow objects fetched with the same private method as above
- Workflow provides a list of launchers it 'has'
- List of ids from workflow is compared with the launcher objects in the projects
- List of actual launcher objects is stored in `@launchers` 

We can tell that it returns a standard HTML view response because there is no explicit `render` line like we saw above in `submit`.

### Views
To find the specific view file used by the `show` action, we look for `show.html.erb` in `apps/dashboard/app/views/workflows/`. 
```erb
# apps/dashboard/app/views/workflows/show.html.erb

<%= javascript_include_tag 'workflows', nonce: true, defer: true %>

<input type="hidden" id="project-id" value="<%= @project.id %>">
<input type="hidden" id="workflow-id" value="<%= @workflow.id %>">
<input type="hidden" id="base-workflow-url" value="<%= project_workflow_path(@project.id, @workflow.id) %>">
<input type="hidden" id="base-launcher-url" value="<%= project_launchers_path(@project.id) %>">

<div id="workflows_app">
  <div class="toolbar" aria-label="toolbar">
    <% hidden_class = @workflow.editable? ? '' : 'd-none' %>
    <%= select_tag "select_launcher", options_from_collection_for_select(@launchers, :id, :title), include_blank: false, class: "form-control w-25 #{hidden_class}" %>
    <button id="btn-add" class="<%= hidden_class %>">Add Launcher</button>
```
In this small snippet, we can see the view using the model objects we stored in variables in `Workflows#show`. 
The top few lines with `type="hidden"` pass relevant data to javascript, like ids and url paths specific to the project and workflow, and further down we see the `@launchers` variable being used to seed a select input.
All of the helper methods seen here, like `project_workflow_path` or `options_from_collection_for_select`, are built-in Rails helpers. See [Action View Helpers](https://guides.rubyonrails.org/action_view_helpers.html) and [Action View Form Helpers](https://guides.rubyonrails.org/form_helpers.html) for an overview of the built-in helpers available in every view.

We can also see from this snippet how Rails views use ERB, or Embedded Ruby. 
For example the line `<% hidden_class = @workflow.editable? ? '' : 'd-none' %>` contains a line of ruby code that stores a string in the `hidden_class` variable, based on the `editable?` method in the Workflow model. 
That `hidden_class` class variable is then added to each element on the page that we want to hide if `@workflow.editable? == false`.

### Models

In the controller and view snippets above, we saw how they identify and store the relevant models (`@project`, `@workflow`, `@launchers`), and how they call methods on these models to determine behavior. 
In the controller case with `Workflows#submit`, we see it use the result of `@workflow.submit(submit_param)` to determine whether to return a success response or a failure response.
In the view, it calls `@workflow.editable?` to determine whether a class is included in certain elements, and by extension, which elements appear on the page.

This is the primary function of models, to manage data and provide endpoints for the controllers and views to interact with this data.
In particular, models are helpful because they isolate the logic and complexity in a single class, allowing us to keep the controllers and views as simple as possible.
```rb
# apps/dashboard/app/models/workflow.rb

  def manifest_file
    Workflow.workflow_dir(@project_dir).join("#{@id}.yml")
  end

  def update(attributes, override = false)
    update_attrs(attributes, override)
    return false unless valid?(:update)

    save_manifest(:update)
  end

  def update_attrs(attributes, override = false)
    [:name, :description, :launcher_ids, :metadata].each do |attribute|
      next unless override || attributes.key?(attribute)
      instance_variable_set("@#{attribute}".to_sym, attributes.fetch(attribute, ''))
    end
  end

  def editable?
    manifest_file.writable? || !shared?(manifest_file)
  end
```
In this small snippet, we get a good overview of what a basic model contains
- `manifest_file` constructs a path where workflow settings are saved as YAML
- `update` is directly used in WorkflowsController to modify workflow settings
- `update_attrs` is an internal method that facilitates the modification
- `editable?` reads the state of the manifest file to determine if the user has permission to overwrite it.

The biggest advantage of MVC is it allows us to independently develop our models, views, and controllers.
This means that as long as the `update` and `editable?` methods continue to exist on the Workflow model, 
we can update the underlying logic (like what makes a workflow 'editable') in a single place, while its interactions (like hiding certain elements when it is not) remain the same. 
