.PHONY: build-demo-app create-network run-mysql run-demo-app

build-demo-app:
	docker build -f php/Dockerfile php -t ilariopierbattista/demo-app:1
	docker push ilariopierbattista/demo-app:1

create-network:
	docker network create demo-net

run-mysql:
	docker run --rm \
        --net demo-net \
        --net-alias mysql.demo-app \
        -e MYSQL_DATABASE=db_demo \
        -e MYSQL_ROOT_PASSWORD=secret-password \
        --mount type=bind,src=`pwd`/dump.sql,dst=/docker-entrypoint-initdb.d/dump.sql \
        --name demo-app_mysql \
        mysql:8

run-demo-app:
	docker run --rm \
        --net demo-net \
        -p 10080:8000 \
        -e DATABASE_URL=mysql://root:secret-password@mysql.demo-app/db_demo \
        --name demo-app_php \
        ilariopierbattista/demo-app:1
