# Worpress-
Projeto 
Objetivo:
  Criar uma ambiente web utilizando:
    Worpress;
    Docker Swarm;
    Grafana;
    Prometheus;
    Redis.

1-Primeiro passo verificar e instalar o docker e o docker compose:
  -sudo apt install docker.io docker-compose -y
2-Após a instalação se inicia o docker swarm:
  -sudo docker swarm init
3-Agora crie uma pasta e uma arquivo para configurar o docker compose e armazena-lô:
  -mkdir wordpress
  -cd wordpress
  -touch docker-compose.yml
  -none docker-compose.yml
    -3.1- Em seguida dentro do docker-compose ira configurar todas as ferramentas:
      -version: '3.8'
            services:
              wordpress:
                image: wordpress:latest
                ports:
                  - "8080:80"
                environment:
                  WORDPRESS_DB_HOST: db:3306
                  WORDPRESS_DB_USER: lucas
                  WORDPRESS_DB_PASSWORD: Lucas@11012006
                  WORDPRESS_DB_NAME: wordpress
                volumes:
                  - wordpress_data:/var/www/html
                deploy:
                  replicas: 1
                  update_config:
                    parallelism: 1
                    delay: 10s
                  restart_policy:
                    condition: on-failure
            
              db:
                image: mysql:5.7
                environment:
                  MYSQL_DATABASE: wordpress
                  MYSQL_USER: lucas
                  MYSQL_PASSWORD: Lucas@11012006
                  MYSQL_ROOT_PASSWORD:
                volumes:
                  - db_data:/var/lib/mysql
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
              redis:
                image: redis:latest
                command: ["redis-server", "--appendonly", "yes"]
                volumes:
                  - redis_data:/data
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
              prometheus:
                image: prom/prometheus:latest
                volumes:
                  - ./prometheus.yml:/etc/prometheus/prometheus.yml
                ports:
                  - "9090:9090"
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
              grafana:
                image: grafana/grafana:latest
                ports:
                  - "3000:3000"
                environment:
                  - GF_SECURITY_ADMIN_PASSWORD=admin
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
              mysql_exporter:
                image: prom/mysqld-exporter:latest
                environment:
                  DATA_SOURCE_NAME: lucas:Lucas@11012006@(db:3306)/wordpress
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
              redis_exporter:
                image: oliver006/redis_exporter:latest
                environment:
                  REDIS_ADDR: redis:6379
                deploy:
                  replicas: 1
                  restart_policy:
                    condition: on-failure
            
            volumes:
              wordpress_data:
              db_data:
              redis_data:
4-Em seguida vai criar o arquivo de configuração do prometheus:
    touch prometheus.yml;
    cd prometheus.yml;
    4.1-global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'mysql'
    static_configs:
      - targets: ['db:9104']
  - job_name: 'redis'
    static_configs:
      - targets: ['redis:9121']

5-Após isso você ira transformar o arquivo do docker compose em um stack file para implantar no swarm:
  - sudo docker stack deploy -c docker-compose.yml wordpress_stack

6-Após isso você ira acessa a locahost:8080 e configurar o seu worpresse e instalar o plugin do "Redis Object Cache";

7-Ápos instalar o plugin vai criar o arquivo wp-config.php e configurar o Redis dentro dele:
  define('WP_CACHE', true);  
  define('WP_REDIS_HOST', 'redis');
  define('WP_REDIS_PORT', 6379);
  define('WP_REDIS_TIMEOUT', 1);
  define('WP_REDIS_READ_TIMEOUT', 1);
  define('WP_REDIS_DATABASE', 0);-
    
8- Conclusão:
  -Após esse processo você deve ter uma ambiente web com os requisíto.
   
