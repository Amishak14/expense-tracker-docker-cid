pipeline {
    agent any
    stages {
        stage("Checkout") {
            steps {

               checkout scm
        }


    }

        stage("update git"){
        steps{
            withCredentials([usernamePassword(credentialsId: 'my-git', passwordVariable: 'pass', usernameVariable: 'user')]) {
                  script {
                        env.encodedPass=URLEncoder.encode(pass, "UTF-8")
                          git config user.email "${GITHUB_EMAIL}"
                          git config user.name "${GITHUB_USERNAME}"
                          "cat manifest.yaml"  
                         echo "${TAG}"
          sed -i 's+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-backend:.*+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-backend:${TAG}+g' manifest.yaml
          sed -i 's+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-frontend:.*+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-frontend:${TAG}+g' manifest.yaml
             "cat manifest.yaml"
            def diffCmd = 'git diff --name-only main..branch'
            def diff = sh(script: diffCmd, returnStdout: true).trim()
            if (diff.empty) {
                echo "No changes found. Skipping pull request creation."
            }
            else
            {
         "git add ."
         "git commit -m 'done by jenkins job docker-pipeline' "
          git push https://$user:$encodedPass@github.com/$user/expense-tracker-cd.git HEAD:branch
             }
                  }}
            
            }
        }

 

       
//           sh "sed -i 's+docker.io/amishark/node-backend:.*+docker.io/amishark/node-backend:${TAG}+g' manifest.yaml"
//           sh "sed -i 's+docker.io/amishark/node-backend:.*+docker.io/amishark/node-frontend:${TAG}+g' manifest.yaml"
       
       // sh "git pull"
        
        
        
         stage("deploy the application") {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject("$PROJECT_NAME") {
                        echo "Using project: ${openshift.project()}"
                         'oc project "$PROJECT_NAME"'
                          'oc apply -f manifest.yaml'
                    }
                 }
            }
        } 
     
       
        
        }
    }
