# Images

## Spark

```sh
# docker build -t multivac-spark:3.2.1 -f images/spark/datamechanics/Dockerfile .
docker build -t multivac-spark:3.2.1 -f images/spark/native/Dockerfile .

docker images | grep multivac-spark

docker tag IMAGE-ID maziyar/multivac-spark:VERSION
docker push maziyar/multivac-spark:VERSION
```

## Zeppelin

```sh
cd ./images/zeppelin/bin
docker build -t multivac-zeppelin:0.10.1 ./

docker images | grep multivac-zeppelin

docker tag IMAGE-ID maziyar/multivac-zeppelin:VERSION
docker push maziyar/multivac-zeppelin:VERSION

cd ./images/zeppelin/zeppelin-interpreter
docker build -t multivac-zeppelin-interpreter:0.10.1 ./

docker images | grep multivac-zeppelin-interpreter

docker tag IMAGE-ID maziyar/multivac-zeppelin-interpreter:VERSION
docker push maziyar/multivac-zeppelin-interpreter:VERSION
```
