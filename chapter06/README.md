# Kind用のingress-nginxマニフェストは、公式から直接適用する
curl -o cluster/ingress-nginx-deploy.yaml https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml