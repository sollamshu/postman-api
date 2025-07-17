# API Testing with Postman and GitHub Actions

This repository sets up a Continuous Integration (CI) workflow to automate the execution of API tests developed in Postman. We leverage **Newman**, Postman's command-line collection runner, and **GitHub Actions** to ensure the quality of our APIs with every code change.

---

## 🚀 How It Works

When the workflow is triggered, GitHub Actions performs the following steps:

1.  **Checkout code**: Downloads the latest version of our repository.
2.  **Set up Node.js**: Installs the necessary environment to run Newman.
3.  **Install Newman**: Downloads and configures Newman, the Postman CLI.
4.  **Run Postman collections**: Newman executes our tests defined in the Postman collections.
5.  **Generate an HTML report**: Creates a detailed report of the test results for easy visualization.
6.  **Upload report**: Attaches the HTML report as a GitHub Actions "artifact," allowing you to download and review it.

---

## 📂 Project Structure

For the workflow to function correctly, your Postman files should be organized as follows:

.
├── .github/
│   └── workflows/
│       └── postman-api-ci.yml  # This is your GitHub Actions workflow file
├── collections/
│   └── Collection.postman_collection.json  # Your Postman collection
├── environments/
│   └── Environment.postman_environment.json  # Your Postman environment (optional)
└── test-results/
└── postman-report.html  # The HTML report will be generated here (no need to create it manually)


* **`collections/`**: Contains your exported Postman collection `.json` files.
* **`environments/`**: Contains your exported Postman environment `.json` files (if you're using them).
* **`test-results/`**: This is the directory where the HTML test report will be saved.

---

## 🛠️ Setup and Usage

Follow these steps to get your automated tests up and running:

### 1. Export Your Postman Collections and Environments

Make sure to export your collections and any associated environments from Postman and save them into the `collections/` and `environments/` directories respectively.

* **Example Collection:** `collections/Collection.postman_collection.json`
* **Example Environment:** `environments/Environment.postman_environment.json`

### 2. Configure Your GitHub Actions Workflow

The workflow file (`.github/workflows/postman-api-ci.yml`) defines the steps for running the tests. Your current setup (`workflow_dispatch`) allows you to manually trigger the workflow from the GitHub interface.

**Example of key commands within the workflow:**

* **Newman Installation:**
    ```bash
    npm install -g newman
    ```
    This command installs Newman globally on the GitHub Actions runner.

* **Running the Postman Collection:**
    ```bash
    newman run collections/Collection.postman_collection.json -e environments/Environment.postman_environment.json
    ```
    This command executes the specified Postman collection. Ensure the path and file name (`Collection.postman_collection.json`) are correct, and include the environment (`-e environments/Environment.postman_environment.json`) if necessary.

* **Generating the HTML Report:**
    ```bash
    npm install -g newman-reporter-htmlextra
    newman run collections/Collection.postman_collection.json -e environments/Environment.postman_environment.json -r htmlextra --reporter-htmlextra-export test-results/postman-report.html
    ```
    Here, we first install the `htmlextra` reporter, then run Newman again, this time specifying that we want an HTML report to be saved in `test-results/postman-report.html`.

* **Uploading the Report as an Artifact:**
    ```yaml
    - name: Upload report
      if: always()
      uses: actions/upload-artifact@v4
      with:
        name: postman-report
        path: test-results/
        retention-days: 30
    ```
    This step is crucial for accessing the report after the workflow completes. The report will be saved as a GitHub "artifact" named `postman-report` and will be available for download for 30 days.

### 3. Run the Workflow

Since your workflow uses `workflow_dispatch`, you can trigger it manually from the **"Actions"** tab in your GitHub repository. Find your workflow's name (`Workflow`) and click on "Run workflow."

---

## 📊 Reviewing Results

Once the workflow has finished, you can review the results:

1.  Go to the **"Actions"** tab in your GitHub repository.
2.  Click on the most recent workflow run.
3.  Within the run summary, look for the **"Artifacts"** section.
4.  Click on **`postman-report`** to download the `postman-report.html` file. Open it in your web browser to view a detailed report of all your tests.
