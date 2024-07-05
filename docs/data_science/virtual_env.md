Virtual environments are useful tools that allow you to maintain a seperate set of packages for each project you are working on. This approach has several advantages over maintaining an central package installation common to all projects:

1. Virtual environments allow you to clearly communicate your code's required dependencies to your collaborators, reducing the time needed to onboard new collaborators.
2. Virtual environments allow you to keep seperate versions of packages for each project, so you can use software only compatible with a specific version of a package.
3. Updating dependencies for one project will never break dependencies for another project.