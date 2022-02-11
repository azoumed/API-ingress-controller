# api-ingress-controller

# I. Custom Header	

Pour configurer, les customs header, ll va falloir modifier/créer la configmap appliquée à l'ingress STANDARD. 

Dans la partie ***data*** de la configmap, il faut rajouter : 

```yaml
data:
  proxy-set-headers: "eot-iot-tld-<env>/<env>-tld-api-custom-headers" 
```

La valeur de la propriété *proxy-set-headers* est composé ainsi : '*namespace*/*configmap name*'

Il faut donc créer une nouvelle configmap *custom-headers* 

```yaml
apiVersion: v1
data:
  x-client-app: "true"
kind: ConfigMap
metadata:
  name: <env>-tld-api-custom-headers
  namespace: eot-iot-tld-<env>
```

Pour deployer le configmap, il faut utilise le command line interface de kubernetes:

```sh
kubectl create -f <nom_de_ficher_config_map>
```

# II. Ingress Controller

Pour installer l'ingress controller, il faut se rendre dans le répertoire Chart et lancer la commande suivante:

```sh
helm3.exe get values aks-nginx-ingress-tld-api-controller > <env>-nginx-ingress-controler-api.yaml
```

Modifier le fichier yaml, comme indique dans la première partie, une fois que les modifications effectuées, lancer la commande suivantes:

```sh
helm3.exe install aks-nginx-ingress-tld-api-controller . -f <env>-nginx-ingress-controler-api.yaml
```

# III. Ingress Resource

Etant donné qu'il y a maintenant deux ingress controller dans le namespace, il faut que chaque ingress resource point vers le controller dedié.

Dans le fichier yaml de l'ingress controller, il faut renommer la valeur de la variable *ingressClass* dans notre cas il a comme valeur *ingress-api*
```yaml
ingressClass: nginx-api
```

Et dans la fichier yamk de l'ingress resource, il faut ajouter l'annotation suivante:

```yaml
kubernetes.io/ingress.class: "nginx-api"
```