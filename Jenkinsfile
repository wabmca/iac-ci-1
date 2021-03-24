pipeline {
  agent {
    docker {
      args ''
      image 'retr0h/molecule'
    }

  }
  stages {
    stage('Role syntax') {
      steps {
        sh '''cd $ROLEDIR
molecule syntax'''
      }
    }
    stage('Role dependecies') {
      steps {
        sh '''cd $ROLEDIR
molecule dependency'''
      }
    }
    stage('Role create') {
      steps {
        sh '''cd $ROLEDIR
molecule create'''
      }
    }
    stage('Role prepare') {
      steps {
        sh '''cd $ROLEDIR
molecule prepare'''
      }
    }
    stage('Role converge') {
      steps {
        sh '''cd $ROLEDIR
molecule converge'''
      }
    }
    stage('Role idempotence') {
      steps {
        sh '''cd $ROLEDIR
molecule idempotence'''
      }
    }
    stage('Role side_effect') {
      steps {
        sh '''cd $ROLEDIR
molecule side-effect'''
      }
    }
    stage('Role verify') {
      steps {
        sh '''cd $ROLEDIR
molecule verify'''
      }
    }
    stage('Stage Ansible run') {
      steps {
        ansiblePlaybook(playbook: 'ansible/site.yml', disableHostKeyChecking: true, colorized: true, inventory: 'ansible/inventory', credentialsId: 'ansiblekey')
      }
    }
    stage('Test infrastructure') {
      environment {
        MOLECULE_INVENTORY_FILE = 'ansible/inventory'
      }
      steps {
        withCredentials(bindings: [sshUserPrivateKey(credentialsId: 'ansiblekey', keyFileVariable: 'KEYFILE')]) {
          sh 'testinfra --ssh-identity-file=${KEYFILE} --connection=ansible --ansible-inventory=${MOLECULE_INVENTORY_FILE} ${ROLEDIR}/molecule/default/tests/test_default.py  '
        }

      }
    }
  }
  environment {
    ROLEDIR = 'ansible/roles/webserver'
  }
}
