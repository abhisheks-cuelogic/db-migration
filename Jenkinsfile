podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'flyway', image: 'boxfuse/flyway:5.1.4', command: 'cat', ttyEnabled: true,
    envVars: [
            envVar(key: 'FLYWAY_URL', value: 'jdbc:mysql://172.21.31.149:3306/flyway'),
            secretEnvVar(key: 'FLYWAY_USER', secretName: 'mysql-user', secretKey: 'FLYWAY_USER'),
            secretEnvVar(key: 'FLYWAY_PASSWORD', secretName: 'mysql-password', secretKey: 'FLYWAY_PASSWORD'),
            envVar(key: 'FLYWAY_LOCATIONS', value: 'filesystem:/home/jenkins/workspace/${env.JOB_NAME}/db-migration/sql-scripts')
        ]
    )
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]
  ) {
    node('mypod') {
        stage('Check running containers') {
            container('docker') {
                // example to show you can run docker commands when you mount the socket
                sh 'hostname'
                sh 'hostname -i'
                sh 'docker ps'
            }
        }
        
        stage('Clone repository') {
            container('git') {
                sh 'whoami'
                sh 'hostname -i'
                sh 'git clone -b master https://github.com/abhisheks-cuelogic/db-migration.git'
            }
        }

        stage('flyway migration') {
            container('flyway') {
                sh """
                env
                flyway info
                flyway migrate
                """
            }
        }
    }
}