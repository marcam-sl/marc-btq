
pipeline {
  agent {
    kubernetes {
      yaml readTrusted('jenkins/pod-templates/CI_shell_pod.yaml')
      defaultContainer "shell"
    }
  }


  parameters {
    string(name: 'APP_NAME', defaultValue: 'public-BTQ', description: 'name of the app (integration build)')
    string(name: 'machine_dns', defaultValue: 'marc.btq.sealights.co', description: 'name of the app (integration build)')
    string(name: 'BRANCH', defaultValue: 'public', description: 'Branch to clone')
    string(name: 'CHANGED_BRANCH', defaultValue: 'changed-branch', description: 'Branch to compare')
    string(name: 'BUILD_BRANCH', defaultValue: 'public', description: 'Branch to Build images that have the creational LAB_ID (send to public branch to build)')
    string(name: 'SL_TOKEN', defaultValue: 'eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJodHRwczovL1BST0QtUE9DLmF1dGguc2VhbGlnaHRzLmlvLyIsImp3dGlkIjoiUFJPRC1QT0MsbmVlZFRvUmVtb3ZlLEFQSUdXLTg2ZDk4ZDJkLTEyYjQtNGU0Zi05ZWI0LTNmNDIwMzdmMWE2MCwxNzA0NjE4NzgzMTM0Iiwic3ViamVjdCI6ImJ0cS1tYXJjQGFnZW50IiwiYXVkaWVuY2UiOlsiYWdlbnRzIl0sIngtc2wtcm9sZSI6ImFnZW50IiwieC1zbC1zZXJ2ZXIiOiJodHRwczovL2J0cS1tYXJjLnNlYWxpZ2h0cy5jby9hcGkiLCJzbF9pbXBlcl9zdWJqZWN0IjoiIiwiaWF0IjoxNzA0NjE4NzgzfQ.Al9R8IYJhdoZGIWJbHMd48kAlv-HqMVO5SUlyYIQCrMcUT0QKbOkDzbEIm6o9c6zleDgVUhTwp0qSbcycREVeJvbMXqrsgOJkjogASa4speHzrAXCMR_1YRktvAzBafIi1f2lVCceNio_B3UbDBxdsFHund-oPpEAJhnwARNeHZ146HFtgGjJHDZZN5CuobXZjl3oGtEl5HZLy8taSclMnNmI0dNAnP-UUNMTkty0pWoYCFySJQH4KFGXGFKTnOhZa862kkNFPRr4_XcXmzbGZD2wxHDD-IQD22cBLfyYRJgdZeME-z03wupM1ec_Shxb4IAPN71IBb9d6hMbgcnDv6Mbe2KLmG0sHQ0K3sGAhX6kb1V6XBp_UZY1QjBTjDp0Y0EDRE9GsidZw2c0g9pkRBGETSlDEYJbPc4FFeJCOCPuQsqJJdB8oUWX2NclvwpyyUr6D0LplTSITvghSvZxdg5YwH1gaqpr6-jInfv7gBVq2oviTv-8NamyWV6Hd-UQ3cUOAeTkSD_OAt8QS8s8b9xzAUnqiOLazO9dVq8NjO6OjVrYFmURYh4ZiWz0INu4UxEtbpOu0ZW0xOlOu_5iySbXB0k5_I3LXdB-YtsbTePi3Pyug9wgWzGzeUQZhFNvQY17sUhHYVMpCm7eJeS-00pBY1Q-i3pXtYSYyO-jWs', description: 'sl-token')
    string(name: 'BUILD_NAME', defaultValue: '', description: 'build name (should change on every build)')
    booleanParam(name: 'Run_all_tests', defaultValue: true, description: 'Checking this box will run all tests even if individual ones are not checked')
    booleanParam(name: 'Cucumber', defaultValue: false, description: 'Run tests using Cucumber testing framework (java)')
    booleanParam(name: 'Cypress', defaultValue: false, description: 'Run tests using Cypress testing framework')
    booleanParam(name: 'Junit_with_testNG', defaultValue: false, description: 'Run tests using Junit testing framework with testNG (maven)')
    booleanParam(name: 'Junit_without_testNG', defaultValue: false, description: 'Run tests using Junit testing framework without testNG (maven)')
    booleanParam(name: 'Junit_with_testNG_gradle', defaultValue: false, description: 'Run tests using Junit testing framework with testNG (gradle)')
    booleanParam(name: 'Mocha', defaultValue: false, description: 'Run tests using Mocha testing framework')
    booleanParam(name: 'MS', defaultValue: false, description: 'Run tests using MS testing framework')
    booleanParam(name: 'NUnit', defaultValue: false, description: 'Run tests using NUnit testing framework')
    booleanParam(name: 'Postman', defaultValue: false, description: 'Run tests using postman testing framework')
    booleanParam(name: 'Pytest', defaultValue: false, description: 'Run tests using Pytest testing framework')
    booleanParam(name: 'Robot', defaultValue: false, description: 'Run tests using Robot testing framework')
    booleanParam(name: 'Soapui', defaultValue: false, description: 'Run tests using Soapui testing framework')
  }

  stages {
    stage('Clone Repository') {
      steps {
        script {
          git branch: params.BRANCH, url: 'https://github.com/Sealights-btq/marc-btq.git'
          
        }
      }
    }
    //Build parallel images
    stage('Build BTQ') {
      steps {
        script {
          env.CURRENT_VERSION = "1-0-${BUILD_NUMBER}"

          build_btq(
            sl_report_branch: params.BRANCH,
            sl_token: params.SL_TOKEN,
            build_name: "1-0-${BUILD_NUMBER}",
            branch: params.BRANCH,
            tag: env.CURRENT_VERSION,
          )
        }
      }
    }

    stage('update-btq') {
      steps {
        script {
          env.CURRENT_VERSION = "1-0-${BUILD_NUMBER}"
          def IDENTIFIER= "${params.BRANCH}-${env.CURRENT_VERSION}"
          env.LAB_ID = create_lab_id(
          token: "${params.SL_TOKEN}",
          machine: "https://btq-marc.sealights.co",
          app: "${params.APP_NAME}",
          branch: "${params.BUILD_BRANCH}",
          test_env: "${IDENTIFIER}",
          lab_alias: "${IDENTIFIER}",
          cdOnly: true,
          )


          build(job: 'update-btq', parameters: [string(name: 'IDENTIFIER', value: "${params.machine_dns}"),
                                                string(name:'tag' , value:"${env.CURRENT_VERSION}"),
                                                string(name:'buildname' , value:"${params.BRANCH}-${env.CURRENT_VERSION}"),
                                                string(name:'labid' , value:"${env.LAB_ID}"),
                                                string(name:'branch' , value:"${params.BRANCH}"),
                                                string(name:'token' , value:"${params.SL_TOKEN}"),
                                                string(name:'sl_branch' , value:"${params.BRANCH}")])
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          run_tests(
            branch: params.BRANCH,
            lab_id: env.LAB_ID,
            token: params.SL_TOKEN,
            machine_dns: "${params.machine_dns}:8081",
            Run_all_tests: params.Run_all_tests,
            Cucumber: params.Cucumber,
            Cypress: params.Cypress,
            Junit_with_testNG: params.Junit_with_testNG,
            Junit_without_testNG: params.Junit_without_testNG,
            Junit_with_testNG_gradle: params.Junit_with_testNG_gradle,
            Mocha: params.Mocha,
            MS: params.Mocha,
            NUnit: params.NUnit,
            Postman: params.Postman,
            Pytest: params.Pytest,
            Robot: params.Robot,
            Soapui: params.Soapui
          )
        }
      }
    }

  }
}

def get_secret (SecretID, Region, Profile="") {
  if (Profile != "") {
    Profile = "--profile ${Profile}"
  }
  String secret_key = "${SecretID.split('/')[-1]}" as String
  def secret_value = (sh(returnStdout: true, script: "aws secretsmanager get-secret-value --secret-id ${SecretID} --region ${Region} ${Profile}| jq -r '.SecretString' | jq -r '.${secret_key}'")).trim()
  return secret_value
}


def build_btq(Map params){
  

  def parallelLabs = [:]
  //List of all the images name
  params.SL_TOKEN= "${params.sl_token}"

  def services_list = ["adservice","cartservice","checkoutservice", "currencyservice","emailservice","frontend","paymentservice","productcatalogservice","recommendationservice","shippingservice"]
  //def special_services = ["cartservice"].
  env.BUILD_NAME= "${params.build_name}" == "" ? "${params.branch}-${env.CURRENT_VERSION}" : "${params.build_name}"

  services_list.each { service ->
    parallelLabs["${service}"] = {
      build(job: 'BTQ-BUILD', parameters: [string(name: 'SERVICE', value: "${service}"),
                                           string(name:'TAG' , value:"${params.tag}"),
                                           string(name:'SL_REPORT_BRANCH' , value:"${params.sl_report_branch}"),
                                           string(name:'BRANCH' , value:"${params.branch}"),
                                           string(name:'BUILD_NAME' , value:"${env.BUILD_NAME}"),
                                           string(name:'SL_TOKEN' , value:"${params.SL_TOKEN}")])
    }
  }
  parallel parallelLabs
}


def run_tests(Map params){
      sleep time: 120, unit: 'SECONDS'
      build(job: "test_runner", parameters: [
        string(name: 'BRANCH', value: "${params.branch}"),
        string(name: 'SL_LABID', value: "${params.lab_id}"),
        string(name: 'SL_TOKEN', value: "${params.token}"),
        string(name: 'MACHINE_DNS', value: "http://${params.machine_dns}"),
        booleanParam(name: 'Run_all_tests', value: Boolean.valueOf(params.Run_all_tests)),
        booleanParam(name: 'Cucumber', value: params.Cucumber),
        booleanParam(name: 'Cypress', value: params.Cypress),
        booleanParam(name: 'Junit_with_testNG', value: params.Junit_with_testNG),
        booleanParam(name: 'Junit_without_testNG', value: params.Junit_without_testNG),
        booleanParam(name: 'Junit_with_testNG_gradle', value: params.Junit_with_testNG_gradle),
        booleanParam(name: 'Mocha', value: params.Mocha),
        booleanParam(name: 'MS', value: params.Mocha),
        booleanParam(name: 'NUnit', value: params.NUnit),
        booleanParam(name: 'Postman', value: params.Postman),
        booleanParam(name: 'Pytest', value: params.Pytest),
        booleanParam(name: 'Robot', value: params.Robot),
        booleanParam(name: 'Soapui', value: params.Soapui)
      ])

}

def set_assume_role(Map params) {
  params.set_globaly = params.set_globaly == null ? true : params.set_globaly
  def credential_map = sh (returnStdout: true, script: """
                                aws sts assume-role --role-arn arn:aws:iam::${params.account_id}:role/${params.role_name}  \\
                                --role-session-name ${params.env}-access --query \"Credentials\"
                            """).replace('"', '').replaceAll('[\\s]', '').trim()

  def map = convert_to_map(credential_map)
  if (params.set_globaly) {
    env.AWS_ACCESS_KEY_ID = "${map.AccessKeyId}"
    env.AWS_SECRET_ACCESS_KEY = "${map.SecretAccessKey}"
    env.AWS_SESSION_TOKEN = "${map.SessionToken}"
  } else {
    return map
  }
}

def create_lab_id(Map params) {
  try {
    def cdOnlyString = ""
    if (params.cdOnly){
      cdOnlyString = ', "cdOnly": true'
    }
    if (params.isPR){
      env.LAB_ID = (sh(returnStdout: true, script:"""
            #!/bin/sh -e +x
            curl -X POST "${params.machine}/sl-api/v1/agent-apis/lab-ids/pull-request" -H "Authorization: Bearer ${params.token}" -H "Content-Type: application/json" -d '{ "appName": "${params.app}", "branchName": "${params.branch}", "testEnv": "${params.test_env}", "targetBranch": "${params.target_branch}", "isHidden": true }' | jq -r '.data.labId'
           """)).trim()
    } else {
      env.LAB_ID = (sh(returnStdout: true, script:"""
            #!/bin/sh -e +x
            curl -X POST "${params.machine}/sl-api/v1/agent-apis/lab-ids" -H "Authorization: Bearer ${params.token}" -H "Content-Type: application/json" -d '{ "appName": "${params.app}", "branchName": "${params.branch}", "testEnv": "${params.test_env}", "labAlias": "${params.lab_alias}", "isHidden": true ${cdOnlyString}}' | jq -r '.data.labId'
           """)).trim()
    }
    echo "LAB ID: ${env.LAB_ID}"
    return env.LAB_ID
  } catch (err) {
    echo env.LAB_ID
    error "Failed to create lab id"
  }
}

def convert_to_map(mapAsString) {
  def map =
    // Take the String value between
    // the [ and ] brackets.
    mapAsString[1..-2]
    // Split on , to get a List.
      .split(',')
    // Each list item is transformed
    // to a Map entry with key/value.
      .collectEntries { entry ->
        def pair = entry.split(':')
        [(pair.first()): "${pair.last()}"]
      }

  return map
}
