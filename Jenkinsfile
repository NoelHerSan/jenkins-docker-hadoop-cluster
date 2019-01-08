node {

    stage 'Checkout'    
        checkout scm

    stage 'Integration Test'
         sh "docker-compose build --no-cache"
         sh "docker-compose -f docker-compose.yml up --force-recreate --abort-on-container-exit"
         sh "docker-compose -f docker-compose.yml down -v"
}
