# Notes

## Docker commands

### Building image

Build with default Dockerfile
```
docker build -t frontend_web .
```

Build with a different Dockerfile
```
docker build -f Dockerfile.dev -t frontend_web .
```

### Running container

Run with port mapping
```
docker run -p 8080:3000 frontend_web:latest
```

Run tests
```
docker run frontend_web:latest npm run test
```

## Docker Compose Commands

Run containers
```
docker-compose up
```

Run and re-build image
```
docker-compose up --build
```

Stop containers
```
docker-compose down
```

