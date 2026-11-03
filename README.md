# Práctica 4: configurar el backend de la calculadora

### Propósito

Desplegar la API de la calculadora y suministrar su ruta de logs mediante un
`Secret`. Dos réplicas muestran que un Service distribuye las solicitudes entre
los Pods y que los datos sensibles o de entorno deben mantenerse fuera de la imagen.

Las credenciales de este ejemplo son ficticias; Base64 no cifra un Secret.
En producción debe existir cifrado, control de acceso y un gestor de secretos externo.

## Archivos

- `calculadora-secret.yaml`: contiene `REQUEST_LOG_FILE` para la API.
- `calculadora-backend.yaml`: despliega dos réplicas del backend.
- `calculadora-service.yaml`: Service `ClusterIP` del backend.

Publica primero la imagen del backend:

```bash
cd ../calculadora-backend
docker build -t tu-registro/calculadora-backend:latest .
docker push tu-registro/calculadora-backend:latest
cd ../practice-4
```

## Crear y comprobar

```bash
kubectl apply -f namespace.yaml
kubectl apply -f calculadora-secret.yaml
kubectl apply -f calculadora-backend.yaml
kubectl apply -f calculadora-service.yaml
kubectl rollout status deployment/calculadora-backend -n practice-4
kubectl get secret,deployments,pods,services -n practice-4
kubectl port-forward service/calculadora-backend 8080:8080 -n practice-4
```

Desde otra terminal:

```bash
curl "http://localhost:8080/api/v1/calculadora/sumar?numero1=7&numero2=5"
curl http://localhost:8080/health
```

Comprueba el Secret sin revelar su contenido:

```bash
kubectl describe secret calculadora-backend -n practice-4
```

## Limpieza

```bash
kubectl delete namespace practice-4
```
