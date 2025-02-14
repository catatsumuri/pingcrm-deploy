RUN

```
docker build -t my-ecs-php-fpm -f ./srv/Dockerfile --target=php-fpm . --no-cache
docker build -t my-ecs-nginx -f ./srv/Dockerfile --target=nginx . 
docker-compose down
docker-compose up -d
```


and Push to ECR

```
aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin ****.dkr.ecr.ap-northeast-1.amazonaws.com

docker tag my-ecs-php-fpm ****.dkr.ecr.ap-northeast-1.amazonaws.com/practice/my-ecs-php-fpm:latest

docker tag my-ecs-nginx ****.dkr.ecr.ap-northeast-1.amazonaws.com/practice/my-ecs-nginx:latest

docker push ****.dkr.ecr.ap-northeast-1.amazonaws.com/practice/my-ecs-php-fpm:latest

docker push ****.dkr.ecr.ap-northeast-1.amazonaws.com/practice/my-ecs-nginx:latest
```

Confirm Image push collectory
```
aws ecr describe-images --repository-name practice/my-ecs-php-fpm --region ap-northeast-1
aws ecr describe-images --repository-name practice/my-ecs-nginx --region ap-northeast-1
```
