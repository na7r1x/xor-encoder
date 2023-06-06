# xor-encoder

A simple web tool for encoding and decoding strings based on the IBM WebSphere XOR Algorithm.

## Usage

### Demo

https://xor.ivnn.dev

### Run locally

Prerequisites: 
- NodeJS 
- npm

```bash
git clone https://github.com/na7r1x/xor-encoder.git
cd xor-encoder
npm install 
npm run serve
```

### Run in a container

```bash
docker run -it -p 8080:80 --rm --name xor-encoder na7r1x/xor-encoder
```

### Deploy to Kubernetes

```bash
git clone https://github.com/na7r1x/xor-encoder.git
```
- For a basic NodePort deployment:

*(Adjust the yaml for the desired nodePort!)*

```bash
kubectl apply -f ./deploy/basic-nodeport.yaml
```

- For an example Nginx Ingress deployment:

*(Adjust `<your hostname>` before applying.)*

```bash
kubectl apply -f ./deploy/basic-ingress-nginx.yaml
```