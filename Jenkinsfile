// Jenkinsfile (Declarative Pipeline)
/* Requires the Docker Pipeline plugin */
pipeline {
    agent any

    tools { go 'Go pipeline' }

    stages {
        stage('Clean old stuff') {
          steps {
            sh '''
              #!/bin/bash

              # Set the directory to search for files
              search_dir="."

              # Set the desired file extension
              file_extension=".yaml"

              # Set the number of most recent entries to keep
              num_to_keep=0

              # Use find command to get all files with the given extension and sort them by modification time (newest first)
              mapfile -t files_to_delete < <(find "$search_dir" -maxdepth 1 -type f -name "*$file_extension" -printf '%T@ %p\n' | sort -nr | tail -n +$((num_to_keep + 1)) | cut -d ' ' -f 2-)

              # Check if there are any files to delete
              if [ ${#files_to_delete[@]} -eq 0 ]; then
                echo "No files found with the extension $file_extension in $search_dir or the number of files to keep is greater than the total number of files."
              else
                # Delete the files
                for file_to_delete in "${files_to_delete[@]}"; do
                  echo "Deleting $file_to_delete"
                  rm "$file_to_delete"
                done
              fi
            '''
          }
        }

        stage('Install weaver') {
          steps {
            sh 'go get github.com/ServiceWeaver/weaver@latest'
            sh 'go install github.com/ServiceWeaver/weaver/cmd/weaver@latest'
            sh 'go install github.com/ServiceWeaver/weaver-kube/cmd/weaver-kube@latest'
          }
        }

        stage('Build Application') {
          steps {
            sh 'weaver generate .'
            sh 'go build .'
            sh 'weaver-kube deploy weaver.toml'
          }
        }

        stage('Deploy Application') {
          steps {
            sh '''
               #!/bin/bash

               # Set the directory to search for files
               search_dir="."

              # Set the desired file extension
              file_extension=".yaml"

              # Use find command to get the newest file with the given extension
              newest_file=$(find "$search_dir" -type f -name "*$file_extension" -printf "%T@ %p\n" | sort -nr | head -n 1 | awk '{print $2}')

              # Apply the newest file
              kubectl apply -f $newest_file

              # Check all the yaml files
              ls -la
            '''
          }
        }
    }
}
