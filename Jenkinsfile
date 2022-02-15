pipeline {

  agent any

  stages {

    stage ('Get latest code') {
      steps {
        sh '''
          rm -rf moleculetest
          git clone https://github.com/mglantz/moleculetest
        '''
      }
    }

    stage ('Setup Python virtual environment') {
      steps {
        sh '''
          pip3.6 install virtualenv
          virtualenv virtenv
          source virtenv/bin/activate
          python3 -m pip install --upgrade ansible testinfra molecule podman python-vagrant ansible-lint flake8 molecule[lint] molecule[podman]
        '''
      }
    }

    stage ('Display versions') {
      steps {
        sh '''
          source virtenv/bin/activate
          python -V
          ansible --version
          molecule --version
          podman --version
        '''
      }
    }

    stage ('Molecule test') {
      steps {
        sh '''
          ls
          source virtenv/bin/activate
          cd moleculetest/wget
          molecule test
        '''
      }
    }

  }

}
