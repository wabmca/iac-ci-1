pipeline {
  stages {
    stage('Role pullimage') {
      steps {
        sh '''export DOCKER_TLS_VERIFY=0
        unset export DOCKER_TLS_VERIFY
        docker -H tcp://0.0.0.0:2375 pull retr0h/molecule'''
      }
    }
    stage('Role syntax') {
      steps {
        sh '''cd $ROLEDIR
export MOLECULE_NO_LOG="false"
molecule --debug syntax'''
      }
    }
    stage('Role dependecies') {
      steps {
        sh '''cd $ROLEDIR
export MOLECULE_NO_LOG="false"
molecule --debug dependency'''
      }
    }

    stage('Role create') {
      steps {
        sh '''cd $ROLEDIR
        export MOLECULE_NO_LOG="false"
        molecule --debug create'''
      }
    }

    stage('Role prepare') {
      steps {
        sh '''cd $ROLEDIR
export MOLECULE_NO_LOG="false"
molecule --debug prepare'''
      }
    }

    stage('Role converge') {
      steps {
        sh '''cd $ROLEDIR
export MOLECULE_NO_LOG="false"
molecule --debug converge'''
      }
    }

    stage('Role idempotence') {
      steps {
        sh '''cd $ROLEDIR
export MOLECULE_NO_LOG="false"
molecule --debug idempotence'''
      }
    }

    stage('Role side_effect') {
      steps {
        sh '''cd $ROLEDIR
molecule  --debug side-effect'''
      }
    }

    stage('Role verify') {
      steps {
        sh '''cd $ROLEDIR
molecule --debug verify'''
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
