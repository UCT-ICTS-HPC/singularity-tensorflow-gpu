// Defining Pipeline 
env.SW_LOCATION = '/opt/exp_soft/singularity-containers/tensorflow'
env.CONTAINER_NAME = 'tensorflow-gpu.sif'
env.CONTAINER_DEF = 'tensorflow-gpu-build.def'
env.TF_VER = 'v1.x'

node('gpu') {

    stage ('Checkout code') {checkout scm}
         
    stage('Build') {
       if (fileExists('container')) {
             echo 'Directory exists'
             } else {
             sh "mkdir container" 
           }
       // Running with --notest as 'singularity build ' does not feature the --nv for GPU and executes %test scriptlet during the build.
       sh "sudo /usr/bin/singularity build --notest container/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER $CONTAINER_DEF"
     }

    stage('Container Cleanup') {
       // Cleaning up unwanted files from the container.
       sh "/usr/bin/singularity cache clean --name container/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER"
     }

    stage('Running Tests') {
       // Execute the %test scriptlet.
       sh "/usr/bin/singularity test --nv container/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER "
     }

    stage('Deliver HPC software to repository') {
      // Make application available to HPC users 
      sh "cp container/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER $SW_LOCATION"
      dir('/opt/exp_soft/singularity-containers/tensorflow') {
         sh "ln -sf $SW_LOCATION/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER tensorflow-$TF_VER-gpu.sif"
      }
      echo "Generating software environment module file"
    }   
}