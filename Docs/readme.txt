Convert the below json to yaml


{
   "id": 101,
   "firstName": "John",
   "lastName": "Doe",
   "email": "john.doe@example.com",
   "phoneNumbers": [
     {"work": "+1234567890"},
     {"home": "+0987654321"},
     {"alternative": "+1122334455"}
   ],
   "salary": 75000,
   "isActive": true,
   "address": {
     "street": "123 Main St",
     "city": "Anytown",
     "state": "CA",
     "zipCode": "90210",
     "country": "USA"
   },
   "department": {
     "id": 10,
     "name": "IT",
     "location": "Building A”
   }
 }


________________


Git Playground url:  http://10.32.2.71:8080?gitplayground
Note: only works in the VM’s provided as lab in the training  




Git Concepts and Commands Guide
1. Commit
Concept
A commit in Git records changes to the repository. Each commit has a unique identifier and includes a commit message describing the changes.
Step-by-Step
1. Create a commit:
 
git commit
   * This command will open your default text editor for you to enter a commit message. Save and close the editor to complete the commit.
2. Branches
Concept
Branches allow you to work on different versions of a project simultaneously. Each branch represents an independent line of development.
Step-by-Step
1. Create a new branch:
git branch dev
   * This creates a new branch named dev.
2. Switch to the new branch:
git checkout dev
   * This command switches to the dev branch.
3. Checkout
Concept
The checkout command is used to switch between branches or to restore files in the working directory to a specific state.
Step-by-Step
1. Switch to a specific commit (detached HEAD state):
git commit
git checkout C1
   * This checks out commit C1, placing you in a detached HEAD state where you're no longer on a branch.
2. Switch to a branch:
git checkout main
   * This switches to the main branch.
4. Merge
Concept
Merging combines changes from one branch into another. It integrates the history of both branches.
Step-by-Step
1. Merge a branch into another:


git checkout -b dev    
git commit  
git checkout main
git commit
git merge dev
   * git checkout main switches to the main branch.
   * git merge dev integrates changes from the dev branch into main.
5. Rebase
Concept
Rebasing is an alternative to merging that allows you to apply commits from one branch onto another, creating a linear history.
Step-by-Step
1. Rebase one branch onto another:
git checkout -b dev    
git commit    
git checkout main    
git commit    
git checkout dev    
git rebase main
git checkout main
git merge dev
git commit
git branch -D dev


   * git checkout dev switches to the dev branch.
   * git rebase main re-applies commits from dev on top of the main branch.
6. Cherry-Pick
Concept
Cherry-picking allows you to apply the changes from a specific commit onto your current branch.
Step-by-Step
1. Cherry-pick specific commits:


git commit
git checkout -b dev
git commit
git checkout main
git commit 
git checkout dev
git commit
git commit
git checkout main
git cherry-pick C5
git commit
1. First workflow:


name: 02-01-Simple Workflow
on: push
jobs: 
  my-first-job:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Hello World"
  my-second-job:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Bye World"




2. Labeler.yml in .github folder
# Add 'Documentation' label to any file changes within ‘Docs' folder
Documentation:
- changed-files:
  - any-glob-to-any-file:
        - Docs/*




3. Contexts github:


name: 08-01-Contexts - github


on:
  workflow_dispatch:


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Display Event Information
      run: |
        echo "Event Name: ${{ github.event_name }}"
        echo "Ref: ${{ github.ref }}"
        echo "SHA: ${{ github.sha }}"
        echo "Actor: ${{ github.actor }}"
        echo "Workflow: ${{ github.workflow }}"
        echo "Run ID: ${{ github.run_id }}"
        echo "Run number: ${{ github.run_number }}"




2. Environment context


name: 08-02-Contexts - env


on:
  workflow_dispatch:


env:
  MY_WORKFLOW_VAR: 'workflow'
  MY_OVERWRITTEN_VAR: 'workflow'


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Print Env Variables
        env:
          MY_OVERWRITTEN_VAR: 'step'
        run: |
          echo "Workflow env: ${{ env.MY_WORKFLOW_VAR }}"
          echo "Overwritten env: ${{ env.MY_OVERWRITTEN_VAR }}"
      - name: Print Env Variables
        run: |
          echo "Workflow env: ${{ env.MY_WORKFLOW_VAR }}"
          echo "Overwritten env: ${{ env.MY_OVERWRITTEN_VAR }}"




3. Input context:


name: 08-03-Contexts - Inputs


on:
  workflow_dispatch:
    inputs:
      somevalue:
        type: string
      debug:
        type: boolean
        default: false


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Use Workflow Input
        run: |
          echo "DEBUG: ${{ inputs.debug }}"


4. Outputs context:


name: 08-04-Contexts - Outputs


on:
  workflow_dispatch:


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Define Workflow Variable
        id: set_var
        run: |
          echo "Setting Workflow Variable"
          echo "name=Max" >> "$GITHUB_OUTPUT"
      - name: Use Workflow Variable
        run: |
          echo "Workflow Variable Value: ${{ steps.set_var.outputs.name }}"






Activity Filters:


name: 07-01-Event Filters and Activity Types


on:
  pull_request:
    types:
      - opened
      - synchronize
    branches:
      - main


jobs:
  echo:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running whenever a PR is opened or synchronized AND base branch is main"




Expressions: 


name: 09-01-Using Expressions
run-name: 09-01-Using Expressions | DEBUG - ${{ inputs.debug && 'ON' || 'OFF' }}


on:
  push:
  workflow_dispatch:
    inputs:
      debug:
        type: boolean
        default: false


jobs:
  echo:
    runs-on: ubuntu-latest
    steps:
      - name: '[debug] Print start-up data'
        if: inputs.debug
        run: |
          echo "Triggered by: ${{ github.event_name }}"
          echo "Branch: ${{ github.ref }}"
          echo "Commit SHA: ${{ github.sha }}"
          echo "Runner OS: ${{ runner.os }}"
      - name: '[debug] Print when triggered from master'
        if: inputs.debug && github.ref == 'refs/heads/master'
        run: echo "I was triggered from master"
      - name: Greeting
        run: echo "Hello, world"


Functions:


name: 11-01-Using-Functions


on:
  pull_request:
  workflow_dispatch:


jobs:
  echo1:
    runs-on: ubuntu-latest
    steps:
      - name: Print PR title
        run: echo "${{ github.event.pull_request.title }}"
      - name: Print PR labels
        run: |
          cat << EOF
          ${{ toJSON(github.event.pull_request.labels) }}
          EOF
      - name: Bug step
        if: ${{ !cancelled() && contains(github.event.pull_request.title, 'fix') }}
        run: echo "I am a bug fix"
      - name: Sleep for 20 seconds
        run: sleep 20
      - name: Failing step
        run: exit 1
      - name: I will be skipped
        if: ${{ success() }}
        run: echo "I will print if previous steps succeed."
      - name: I will execute
        if: ${{ failure() }}
        run: echo "I will print if any previous step fails."
      - name: I will execute
        if: ${{ !cancelled() }}
        run: echo "I will always print, except when the workflow is cancelled."
      - name: I will execute when cancelled
        if: ${{ cancelled() }}
        run: echo "I will print if the workflow has been cancelled."




Execution Flow:


name: 12-01-Controlling the Execution Flow


on:
  workflow_dispatch:
    inputs:
      pass-unit-tests:
        type: boolean
        description: Whether unit tests will pass or not
        default: true


jobs:
  lint-build:
    runs-on: ubuntu-latest
    steps:
      - name: Lint and build
        run: echo "Linting and building project"
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - name: Running unit tests
        run: echo "Running tests..."
      - name: Failing tests
        if: ${{ !inputs.pass-unit-tests }}
        run: exit 1
  deploy-nonprod:
    runs-on: ubuntu-latest
    needs:
      - lint-build
      - unit-tests
    steps:
      - name: Deploying to nonprod
        run: echo "Deploying to nonprod..."
  e2e-tests:
    runs-on: ubuntu-latest
    needs:
      - deploy-nonprod
    steps:
      - name: Running E2E tests
        run: echo "Running E2E tests"
  load-tests:
    runs-on: ubuntu-latest
    needs:
      - deploy-nonprod
    steps:
      - name: Running load tests
        run: echo "Running load tests"
  deploy-prod:
    runs-on: ubuntu-latest
    needs:
      - e2e-tests
      - load-tests
    steps:
      - name: Deploying to prod
        run: echo "Deploying to prod..."
________________


Custom actions:


Composite Custom Action:


Action.yaml file: 
—---------------------------------------------------------------------------------------------------


name: ANZ Setup Java
description: This action allows caching both Java and Maven Dependencies based on the pom.xml file


inputs: 
  java-version:
    description: 'Java version to use'
    default: '11'
    required: true
  working-dir:
    description: The working directory of the application
    default: .
    required: false


runs:
  using: 'composite'
  steps: 
    # Step 1: Setup Java version
    - name: Setup Java version ${{ inputs.java-version }}
      uses: actions/setup-java@v4
      with:
        distribution: 'adopt'
        java-version: ${{ inputs.java-version }}
        cache: 'maven'  # Cache Maven dependencies


    # Step 2: Install Dependencies
    - name: Install Dependencies
      run: mvn dependency:go-offline
      shell: bash
      working-directory: ${{ inputs.working-dir }}  # Set working directory for Maven command
—----------------------------------------------------------------------------------------------------------------------------


Customactions-composite.yml


—----------------------------------------------------------------------------------------------------------------------------


name: CustomActions-Composite
run-name: CustomActions-Composite


on:
  workflow_dispatch:


env:
  working-directory: ./  # Default working directory


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # Step 1: Checkout repository
      - name: checkout repository
        uses: actions/checkout@v4


      # Step 2: Setup Java & Maven dependencies
      - name: Setup Java & Maven dependencies
        uses: ./.github/actions/composite-cache-deps
        with:
          java-version: 11
          working-dir: ${{ env.working-directory }}  # Use specified working directory


      # Step 3: Run tests
      - name: Run tests
        run: mvn test


      # Step 4: Build with Maven
      - name: Build with Maven
        run: mvn clean install
—----------------------------------------------------------------------------------------------------------------------------




Docker Custom Action:


main.py
—----------------------------------------------------------------------------------------------------------------------------
import os
import requests
import time


def ping_url(url,delay,max_trials):
    trials = 0


    while trials < max_trials:
        try:
            response = requests.get(url)
            if response.status_code == 200:
                print(f"Website {url} is reachable.")
                return True
        except requests.ConnectionError:
            print(f"Website {url} is unreachable. Retrying in {delay} seconds...")
            time.sleep(delay)
            trials += 1
        except requests.exceptions.MissingSchema:
            print(f"Invalid URL format: {url}. Make sure the URL has a valid schema (e.g., http:// or https://)")
            return False
        
    return False


def run():
    website_url = os.getenv("INPUT_URL")
    delay = int(os.getenv("INPUT_DELAY"))
    max_trials = int(os.getenv("INPUT_MAX_TRIALS"))
    
    website_reachable = ping_url(website_url,delay,max_trials)


    if not website_reachable:
        raise Exception(f"Website {website_url} is malformed or unreachable.")
    
    print(f"Website {website_url} is reachable.")


if __name__ == "__main__":
    run()


—----------------------------------------------------------------------------------------------------------------------------




Dockerfile
—----------------------------------------------------------------------------------------------------------------------------




FROM python:alpine3.19


WORKDIR /app


COPY requirements.txt .


RUN python -V
RUN pip install --no-cache-dir -r requirements.txt


COPY . .


CMD [ "python", "/app/main.py" ]
—----------------------------------------------------------------------------------------------------------------------------
requirements.txt
—----------------------------------------------------------------------------------------------------------------------------
certifi==2023.11.17
charset-normalizer==3.3.2
idna==3.6
requests==2.31.0
urllib3==2.1.0


—----------------------------------------------------------------------------------------------------------------------------
action.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Ping URL
description: Ping URL until maximum trials have exceeded. If result is not 200 until then, fails the action.


inputs:
  url: # INPUT_URL
    description: URL to ping.
    required: true
  max_trials: # INPUT_MAX-TRIALS
    description: Maximum number of trials until action fails.
    default: '10'
    required: false
  delay:
    description: Delay in seconds between trials.
    default: '5'
    required: false


runs:
  using: docker
  image: Dockerfile


—----------------------------------------------------------------------------------------------------------------------------
CustomActions-Docker.yaml
—----------------------------------------------------------------------------------------------------------------------------
name: CustomActions-Docker


on:
  workflow_dispatch:
    inputs:
      url:
        type: choice
        options:
          - 'https://www.google.com'
          - 'https://www.invalid-aidmdkjmcdkx.com'
          - 'invalid-aidmdkjmcdkx'
      max_trials:
        description: Maximum trials until action fails.
        default: '10'
        required: false
      delay:
        description: Delay in seconds between trials.
        default: '5'
        required: false


jobs:
  ping-url:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Ping URL
        uses: ./.github/actions/docker-ping-url
        with:
          url: ${{ inputs.url }}
          max_trials: ${{ inputs.max_trials }}
          delay: ${{ inputs.delay }}


—----------------------------------------------------------------------------------------------------------------------------


Self Hosted Runners


Run the below command on powershell after opening as run as administrator:


Set-ExecutionPolicy -ExecutionPolicy RemoteSigned


Selfhosted.yml


—----------------------------------------------------------------------------------------------------------------------------


name: selfhostedrunner
on: workflow_dispatch
jobs: 
  my-first-job:
    runs-on: self-hosted
    steps:
      - run: dir


—----------------------------------------------------------------------------------------------------------------------------


Cacheexample.yml
—----------------------------------------------------------------------------------------------------------------------------


name: 15-01-Caching


on:
  workflow_dispatch


jobs:
  install-dependencies:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: '11'
          cache: 'maven'


      - name: Install Dependencies
        run: mvn dependency:go-offline


  test:
    runs-on: ubuntu-latest
    needs: install-dependencies
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: '11'
          cache: 'maven'


      - name: Test with Maven
        run: mvn -B test


  build:
    runs-on: ubuntu-latest
    needs: install-dependencies
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: '11'
          cache: 'maven'


      - name: Build with Maven
        run: mvn clean install -Dmaven.test.skip=true
—----------------------------------------------------------------------------------------------------------------------------
Artifacts.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Artifacts


on:
  workflow_dispatch


jobs:
  upload-artifact:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: '11'
          cache: 'maven'


      - name: Build with Maven
        run: mvn clean install


      - run: mkdir staging && cp target/*.jar staging
      - uses: actions/upload-artifact@v4
        with:
          name: Package
          path: staging


  download-artifact:
    runs-on: ubuntu-latest
    needs: upload-artifact


    steps:
    - name: Download web-app content
      uses: actions/download-artifact@v4
      with:
        name: Package
           
    - name: View content
      run: ls -R


—----------------------------------------------------------------------------------------------------------------------------


Releases.yml


—----------------------------------------------------------------------------------------------------------------------------
name: Release


on:
  workflow_dispatch


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: '11'
          cache: 'maven'


      - name: Build with Maven
        run: mvn clean install -Dmaven.test.skip=true
  
      - run: mkdir staging && cp target/*.jar staging
      - uses: actions/upload-artifact@v4
        with:
          name: Package
          path: staging


  release:
    runs-on: ubuntu-latest
    needs: build


    steps:
    - name: Download web-app content
      uses: actions/download-artifact@v4
      with:
        name: Package
           
    - name: View content
      run: ls -R


    - name: Archive site content
      uses: thedoctor0/zip-release@master
      with:
        filename: app.zip
        
    - name: Create GitHub release
      id: create-new-release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.run_number }}
        release_name: Release ${{ github.run_number }}
        
    - name: Upload release asset
      uses: actions/upload-release-asset@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        upload_url: ${{ steps.create-new-release.outputs.upload_url }}
        asset_path: ./app.zip
        asset_name: app-v${{ github.ref_type }}.zip
        asset_content_type: application/zip


—----------------------------------------------------------------------------------------------------------------------------


________________


Matrices.yml


—----------------------------------------------------------------------------------------------------------------------------


# Workflow for Testing applications on different environments in a single workflow


name: Matrices


on:
  workflow_dispatch


jobs:
  backwards-compatibility:
    name: ${{ matrix.os }}-${{ matrix.java-version }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        java-version: [11,14,17]
        os:
          - ubuntu-latest
          - windows-latest


    steps:
      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'adopt'
          java-version: ${{ matrix.java-version }}
      - name: Perform some tests
        run: echo "Running tests on OS ${{ matrix.os }} and Java ${{ matrix.java-version }}"


—----------------------------------------------------------------------------------------------------------------------------
shellscript.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Shell-Script


on:
  workflow_dispatch


jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Make script executable and run
        run: |
          chmod +x ./init.sh
          ./init.sh
        shell: bash
—----------------------------------------------------------------------------------------------------------------------------
APICall.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Fetch-Data-API
on:
  workflow_dispatch: 


jobs:
  fetch-todo:
    runs-on: ubuntu-latest
    steps:
      - name: Fetch Todo Data
        run: |
          # Use curl to fetch the data from the API endpoint
          # Integrate external systems within the Actions workflow
          # prepare API request with the required inputs like token payload etc
          todo_data=$(curl -s https://jsonplaceholder.typicode.com/todos/1)
          # Print the fetched data
          echo "Fetched Todo Data: $todo_data"
—----------------------------------------------------------------------------------------------------------------------------
docker.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Docker


on:
  workflow_dispatch:
      
jobs:
  docker-build:
    runs-on: ubuntu-latest


    steps:


    - name: Checkout repository
      uses: actions/checkout@v2
           
    - name: Set up JDK
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
        cache: maven


    - name: Build with Maven
      run: mvn clean install
      
    - name: Build Docker image
      run: docker build -t username/app .


    - name: Log in to Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin


    - name: Push image to Docker Hub
      run: docker push username/app


  —----------------------------------------------------------------------------------------------------------------------------
In Command Prompt: docker run -d –rm -p 9090:8081 username:imagename:version 


In browser: localhost:9090\swagger-ui\index.html


In Command Prompt:


$ notepad kubeyml.yml
$ kubectl apply -f. \kubeyml.yml
$ kubectl get pods
$ kubectl delete -f. \kubeyml.yml


—----------------------------------------------------------------------------------------------------------------------------
Todoapp pom.xml
—----------------------------------------------------------------------------------------------------------------------------


<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-parent</artifactId>
                <version>2.7.12</version>
                <relativePath/> <!-- lookup parent from repository -->
        </parent>
        <groupId>com.company</groupId>
        <artifactId>todoapp</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>todoapp</name>
        <description>Manage todos</description>
        <properties>
                <java.version>11</java.version>
                <sonar.java.binaries>${project.build.directory}/classes</sonar.java.binaries>
                <sonar.junit.reportPaths>target/surefire-reports</sonar.junit.reportPaths>
                <!-- JaCoCo Properties -->
                <jacoco.version>0.8.6</jacoco.version>
                <sonar.java.coveragePlugin>jacoco</sonar.java.coveragePlugin>
                <sonar.dynamicAnalysis>reuseReports</sonar.dynamicAnalysis>
                <sonar.jacoco.reportPath>${project.basedir}/../target/jacoco.exec</sonar.jacoco.reportPath>
                <sonar.language>java</sonar.language>
        </properties>
        <dependencies>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-web</artifactId>
                </dependency>
                <dependency>
                        <groupId>org.springdoc</groupId>
                        <artifactId>springdoc-openapi-ui</artifactId>
                        <version>1.6.4</version>
                </dependency>


                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-devtools</artifactId>
                        <scope>runtime</scope>
                        <optional>true</optional>
                </dependency>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-test</artifactId>
                        <scope>test</scope>
                </dependency>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-validation</artifactId>
                </dependency>
                   <dependency>
                        <groupId>com.h2database</groupId>
                        <artifactId>h2</artifactId>
                        <scope>runtime</scope>
                </dependency>  
                <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java 
                 <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.33</version>
                </dependency>  -->
                                
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-data-jpa</artifactId>
                </dependency>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-actuator</artifactId>
                </dependency>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-security</artifactId>
                </dependency>
                <dependency>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-starter-hateoas</artifactId>
                </dependency>
        </dependencies>


        <build>
                <plugins>
                        <plugin>
                                <groupId>org.springframework.boot</groupId>
                                <artifactId>spring-boot-maven-plugin</artifactId>
                        </plugin>
                        <plugin>
                                <groupId>org.sonarsource.scanner.maven</groupId>
                                <artifactId>sonar-maven-plugin</artifactId>
                                <version>3.7.0.1746</version>
                        </plugin>
                        <plugin>
                                <groupId>org.jacoco</groupId>
                                <artifactId>jacoco-maven-plugin</artifactId>
                                <version>${jacoco.version}</version>
                                <executions>
                                        <execution>
                                                <id>jacoco-initialize</id>
                                                <goals>
                                                        <goal>prepare-agent</goal>
                                                </goals>
                                        </execution>
                                        <execution>
                                                <id>jacoco-site</id>
                                                <phase>package</phase>
                                                <goals>
                                                        <goal>report</goal>
                                                </goals>
                                        </execution>
                                </executions>
                        </plugin>
                </plugins>
        </build>


</project>


—----------------------------------------------------------------------------------------------------------------------------




CI Pipeline.yml
—----------------------------------------------------------------------------------------------------------------------------
name: Continuous Integration


on:
  # push:
  #   branches:
  #     - main
  workflow_dispatch:


jobs:
  checkout:
    runs-on: self-hosted
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Shallow clones should be disabled for a better relevancy of analysis


  cache:
    runs-on: self-hosted
    needs: checkout
    steps:
      - name: Cache SonarQube packages
        uses: actions/cache@v4.0.2
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar
          restore-keys: ${{ runner.os }}-sonar
          
      - name: Cache Maven packages
        uses: actions/cache@v4.0.2
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
          restore-keys: ${{ runner.os }}-m2


  jacoco:
    runs-on: ubuntu-latest  # Change to Linux-based runner
    needs: cache
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Set up Maven
        uses: stCarolas/setup-maven@v5
        with:
          maven-version: 3.8.2
        
      - name: Build with Maven
        run: mvn -B package -Pcoverage


      - name: Generate JaCoCo badge
        id: jacoco
        uses: cicirello/jacoco-badge-generator@v2
        with:
          badges-directory: badges
          generate-branches-badge: true
          generate-summary: true


      - name: Log coverage percentages to workflow output
        run: |
          echo "coverage = ${{ steps.jacoco.outputs.coverage }}"
          echo "branches = ${{ steps.jacoco.outputs.branches }}"


      - name: Upload JaCoCo coverage report as a workflow artifact
        uses: actions/upload-artifact@v4.4.0
        with:
          name: jacoco-report
          path: target/site/jacoco/


  sonarqube:
    runs-on: self-hosted
    needs: jacoco
    steps:
      - name: Set up JDK 17
        uses: actions/setup-java@v1
        with:
          java-version: 17
      
      - name: Set up Maven
        uses: stCarolas/setup-maven@v5
        with:
          maven-version: 3.8.2


      - name: Check SonarQube accessibility
        shell: powershell
        run: |
          $Response = Invoke-WebRequest -Uri http://localhost:9000 -Method Head -ErrorAction Stop
          if ($Response.StatusCode -eq 200) {
              Write-Output "SonarQube is accessible."
          } else {
            Write-Output "SonarQube is not accessible."
            exit 1
          }


      - name: Download JaCoCo coverage report
        uses: actions/download-artifact@v4.1.8
        with:
          name: jacoco-report
          path: target/site/jacoco/  # Ensure this matches the upload path


      - name: Build and analyze
        run: mvn -B verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar


  artifacts:
    runs-on: self-hosted
    needs: sonarqube
    steps:
      - name: Copy JAR file to staging
        run: |
          mkdir staging
          Copy-Item target\*.jar staging
        shell: powershell
        
      - uses: actions/upload-artifact@v4
        with:
          name: Package
          path: staging


  release:
    runs-on: ubuntu-latest
    needs: artifacts
    steps:
      - name: Download web-app content
        uses: actions/download-artifact@v4.1.8
        with:
          name: Package
          
      - name: View content
        run: ls -R


      - name: Archive site content
        uses: thedoctor0/zip-release@master
        with:
          filename: app.zip


      - name: Create GitHub release
        id: create-new-release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref_type }}
          release_name: Release ${{ github.ref_type }}


      - name: Upload release asset
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create-new-release.outputs.upload_url }}
          asset_path: ./app.zip
          asset_name: app-v${{ github.ref_type }}.zip
          asset_content_type: application/zip
          
  docker:
    runs-on: ubuntu-latest  # Changed to ubuntu-latest
    needs: release
    steps:
      - name: Checkout code
        uses: actions/checkout@v4


      - name: Set up Maven
        uses: stCarolas/setup-maven@v5
        with:
          maven-version: 3.8.2
        
      - name: Build with Maven
        run: mvn -B package
        
      - name: Build Docker image
        run: docker build -t vijaynvb/todoapp .


      - name: Log in to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin


      - name: Push image to Docker Hub
        run: docker push vijaynvb/todoapp


—----------------------------------------------------------------------------------------------------------------------------
CD main.tf  terraform configuration file
—----------------------------------------------------------------------------------------------------------------------------
provider "google" {
  project = var.project_id
  region  = var.region
}


resource "google_container_cluster" "primary" {
  name     = var.cluster_name
  location = var.location


  initial_node_count = 1


  node_config {
    machine_type = "e2-medium"
    oauth_scopes = [
      "https://www.googleapis.com/auth/cloud-platform",
    ]
    service_account = var.service_account
    
    # Set the disk size to 200GB
    disk_size_gb = 200
  }


  deletion_protection = false 
}


variable "project_id" {
  description = "The ID of the GCP project"
}


variable "region" {
  description = "The GCP region"
}


variable "location" {
  description = "The GCP zone or location"
}


variable "cluster_name" {
  description = "The name of the GKE cluster"
}


variable "service_account" {
  description = "The service account for the GKE cluster"
}


—----------------------------------------------------------------------------------------------------------------------------
CD deployment.yml, 
—----------------------------------------------------------------------------------------------------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todoapih2
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo
  template:
    metadata:
      labels:
        app: todo
    spec:
      containers:
      - name: todoapih2
        image: padmanabhan1/todoapp
        ports:
        - containerPort: 8081 




---


apiVersion: v1
kind: Service
metadata:
  name: svctodoh2api
spec:
  selector:
    app: todo
  ports:
    - port: 80        
      targetPort: 8081 
  type: LoadBalancer  


—----------------------------------------------------------------------------------------------------------------------------
CD cd.yml, 
—----------------------------------------------------------------------------------------------------------------------------
name: Continuous Deployment


on:
  workflow_run:
    workflows: ["Continuous Integration"]
    types:
      - completed
  workflow_dispatch:


env:
  CREDENTIALS: ${{ secrets.GCP_SA_KEY }} 
  PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
  GKE_CLUSTER: gke-cluster   # cluster name
  GKE_REGION: us-east1
  GKE_LOCATION: us-east1-b     # cluster location
  SERVICE_ACCOUNT: ${{ secrets.GCP_SERVICE_ACCOUNT }}
  TFSTATE_CACHE_KEY: terraform-state-${{ github.sha }}


jobs:
  create-cluster:
    # if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest 
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4


      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1


      - name: Terraform Init
        run: terraform init
        env:
          GOOGLE_CREDENTIALS: ${{ env.CREDENTIALS }} 


      - name: Terraform Plan
        run: terraform plan -input=false
        env:
          GOOGLE_CREDENTIALS: ${{ env.CREDENTIALS }}
          TF_VAR_project_id: ${{ env.PROJECT_ID }}
          TF_VAR_region: ${{ env.GKE_REGION }}
          TF_VAR_location: ${{ env.GKE_LOCATION }}
          TF_VAR_cluster_name: ${{ env.GKE_CLUSTER }}
          TF_VAR_service_account: ${{ env.SERVICE_ACCOUNT }}


      - name: Terraform Apply
        id: terraform-apply
        run: terraform apply -auto-approve -input=false
        env:
          GOOGLE_CREDENTIALS: ${{ env.CREDENTIALS }}
          TF_VAR_project_id: ${{ env.PROJECT_ID }}
          TF_VAR_region: ${{ env.GKE_REGION }}
          TF_VAR_location: ${{ env.GKE_LOCATION }}
          TF_VAR_cluster_name: ${{ env.GKE_CLUSTER }}
          TF_VAR_service_account: ${{ env.SERVICE_ACCOUNT }}


      - name: Cache Terraform State
        uses: actions/cache@v3
        with:
          path: |
            .terraform/
            terraform.tfstate
            terraform.tfstate.backup
          key: ${{ env.TFSTATE_CACHE_KEY }}


  deploy-to-cluster:
    runs-on: ubuntu-latest
    needs: create-cluster
    outputs:
      baseurl: ${{ steps.extract-url.outputs.baseurl }}
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4


      - name: Install Google Cloud SDK
        uses: 'google-github-actions/auth@v2'
        with:
          credentials_json: ${{ env.CREDENTIALS }}


      - name: Set up Cloud SDK
        uses: 'google-github-actions/setup-gcloud@v2'


      - name: Configure kubectl to use gke-gcloud-auth-plugin
        run: gcloud components install kubectl


      - name: Get GKE credentials
        run: gcloud container clusters get-credentials ${{ env.GKE_CLUSTER }} --zone ${{ env.GKE_LOCATION }} --project ${{ secrets.GCP_PROJECT_ID }}


      - name: Deploy application to GKE
        run: kubectl apply -f deployment.yaml


      - name: Wait for the service to be ready
        run: |
          echo "Waiting for 60 seconds..."
          sleep 60


      - name: Extract baseurl from GKE service
        id: extract-url
        run: |
          EXTERNAL_IP=$(kubectl get svc svctodoh2api -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
          PORT=$(kubectl get svc svctodoh2api -o jsonpath='{.spec.ports[0].port}')
          echo "EXTERNAL_IP is: $EXTERNAL_IP"
          echo "PORT is: $PORT"
          echo "baseurl=http://$EXTERNAL_IP:$PORT" >> $GITHUB_OUTPUT




  run-postman-tests:
    runs-on: ubuntu-latest
    needs: deploy-to-cluster
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4


      - name: Install Newman
        run: npm install -g newman


      - name: Run Postman Collection
        env:
          BASEURL: ${{ needs.deploy-to-cluster.outputs.baseurl }}
        run: |
          newman run postman_collection.json --env-var "base_url=${{ env.BASEURL }}"
          
  destroy-cluster:
    runs-on: ubuntu-latest
    needs: [deploy-to-cluster, run-postman-tests]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4


      - name: Restore Terraform State Cache
        uses: actions/cache@v3
        with:
          path: |
            .terraform/
            terraform.tfstate
            terraform.tfstate.backup
          key: ${{ env.TFSTATE_CACHE_KEY }}


      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1


      - name: Terraform Init
        run: terraform init
        env:
          GOOGLE_CREDENTIALS: ${{ env.CREDENTIALS }}


      - name: Terraform Destroy
        run: terraform destroy -auto-approve -input=false
        env:
          GOOGLE_CREDENTIALS: ${{ env.CREDENTIALS }}
          TF_VAR_project_id: ${{ env.PROJECT_ID }}
          TF_VAR_region: ${{ env.GKE_REGION }}
          TF_VAR_location: ${{ env.GKE_LOCATION }}
          TF_VAR_cluster_name: ${{ env.GKE_CLUSTER }}
          TF_VAR_service_account: ${{ env.SERVICE_ACCOUNT }}






—----------------------------------------------------------------------------------------------------------------------------