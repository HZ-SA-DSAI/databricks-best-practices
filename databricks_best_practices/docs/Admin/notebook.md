# Notebook Usage

1. [Set up Databricks Repos](https://docs.databricks.com/notebooks/best-practices.html#step-1-set-up-databricks-repos)
    1. Provide your GitHub credentials
    2. Connect to your GitHub repo
2. [Import and run the notebook](https://docs.databricks.com/notebooks/best-practices.html#step-2-import-and-run-the-notebook)
    1. Create a working branch in the repo

        If you’re not familiar with working in [Git branches, see Git Branches - Branches](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) in a Nutshell on the Git website.

    2. Import the notebook into the repo
    3. [Check in and merge the notebook](https://docs.databricks.com/notebooks/best-practices.html#step-24-check-in-and-merge-the-notebook)
    
3. Move code into a shared module

    You can move some of the code in your notebook into a set of shared functions outside of your notebook. This enables you to use these functions with other similar notebooks, which can speed up future coding and help ensure more predictable and consistent notebook results. Sharing this code also enables you to more easily test these functions, which as a software engineering best practice can raise the overall quality of your code as you go.

    1. Create another working branch in the repo
    2. Import the notebook into the repo
    3. Add the notebook’s supporting shared code functions
    4. Add the shared code’s dependencies

         Declaring dependencies improves reproducibility by using precisely defined versions of libraries.

4. Test the shared code
5. Create a job to run the notebooks
6. Set up the repo to test the code and run the notebook automatically whenever the code changes
7. Update the shared code in GitHub to trigger tests

