/* pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
               build 'pipeline'
            }
        }
    

        stage('Test') {
            steps {
                echo 'Hello World'
            }
        }
    
    
        stage('Deploy') {
            steps {
                echo 'Hello World'
            }
        }
    
}} */
pipeline {
    agent any
        
    
    stages {
         stage('Restore nuget') {
            steps {
              
              
                bat 'dotnet restore' //for .NET core
                bat 'nuget restore ProductStore.sln' // for .NET framework
               
            }
        }
        stage('Build') {
            steps {
               
                bat 'dotnet build --configuration Release ./ProductStore.NetCoreApp/ProductStore.NetCoreApp.csproj'           
                bat 'msbuild ProductStore.sln /target:ProductStore_NetFrameworkApp /p:Configuration=Release'
               
            }
        }
        stage('Test') {
            steps {
                bat 'dotnet test --logger trx ./ProductStore.NetCoreApp.Test/ProductStore.NetCoreApp.Test.csproj'
            }
             post {
                    always {
                      //plugin: https://plugins.jenkins.io/mstest/
                        mstest testResultsFile:"**/*.trx", keepLongStdio: true
                    }
        
                }           
        }
        
        stage('Deploy') {
            steps {
                bat 'mkdir deploy'
                bat 'dotnet publish --self-contained --runtime win-x64 -c Release ./ProductStore.NetCore/ProductStore.NetCore.csproj -o ./deploy/BigProject.NetCore'            
           	    //NOT BE TESTED YET
                bat 'msbuild BigProject.sln /target:BigProject_NetFrameworkApp /p:Configuration=Release /p:DeployOnBuild=True /p:DeployDefaultTarget=WebPublish /p:WebPublishMethod=FileSystem /p:DeleteExistingFiles=True /p:publishUrl=./deploy/BigProject.NetFrameworkApp'
                archiveArtifacts artifacts: 'deploy/**', fingerprint: true
                
            }
        }
    }
}
