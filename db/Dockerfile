FROM mysql:5.7

ENV MYSQL_ROOT_PASSWORD=password 
ENV MYSQL_DATABASE=flask-db

# Add the content of the .sql file to your image
# All scripts copied to docker-entrypoint-initdb.d/ are automatically
# executed during container startup
COPY CreateTable.sql /docker-entrypoint-initdb.d
