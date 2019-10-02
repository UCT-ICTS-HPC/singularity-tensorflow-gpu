// Defining Pipeline 
env.SW_LOCATION = '/opt/exp_soft/singularity-containers/tensorflow'
env.CONTAINER_NAME = 'tensorflow-gpu.sif'
env.CONTAINER_DEF = 'tensorflow-gpu-build.def'
env.CONTAINER_DIR = 'container'
env.TF_VER = 'v1.x'

node('gpu') {

    stage ('Checkout code') {checkout scm}
         
    stage('Build') {
       if (fileExists $CONTAINER_DIR) {
             echo 'Directory exists'
             } else {
             sh "mkdir $CONTAINER_DIR" 
           }
       // Running with --notest as 'singularity build ' does not feature the --nv for GPU and executes %test scriptlet during the build.
       sh "sudo /usr/bin/singularity build --notest $CONTAINER_DIR/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER $CONTAINER_DEF"
     }

    stage('Container Cleanup') {
       // Cleaning up unwanted files from the container.
       sh "/usr/bin/singularity cache clean --name $CONTAINER_DIR/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER"
     }

    stage('Running Tests') {
       // Execute the %test scriptlet.
       sh "/usr/bin/singularity test --nv $CONTAINER_DIR/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER "
     }

    stage('Deliver HPC software to repository') {
      // Make application available to HPC users 
      sh "cp $CONTAINER_DIR/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER $SW_LOCATION"
      dir('${SW_LOCATION') {
         sh "ln -sf $SW_LOCATION/$TF_VER-$CONTAINER_NAME-$BUILD_NUMBER tensorflow-$TF_VER-gpu.sif"
      }
      echo "Generating software environment module file"
    }   
}