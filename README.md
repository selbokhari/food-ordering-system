# Système de commande de nourriture / Food Ordering System

## Description

Ce système est un exemple complet et fonctionnel d'un système de demande de repas utilisant des technologies modernes et évolutives.

## Technologies

* Développement piloté par les événements (Event Driven Development)
* Architecture hexagonale
* Saga Pattern
* Kafka comme Event Sourcing
* Schema Registry / Avro
* Kafka Manager
* PostgreSQL

![Architecture de l'application](https://raw.githubusercontent.com/selbokhari/food-ordering-system/main/architecture.png)

## Utilisation

1. Démarrer Zookeeper: `docker-compose -f common.yml -f zookeeper.yml up`
2. Démarrer le cluster Kafka: `docker-compose -f common.yml -f kafka_cluster.yml up`
3. Initialiser les topics Kafka: `docker-compose -f common.yml -f init_kafka.yml up`
4. Vérifier le statut de Zookeeper: `echo ruok | nc localhost 2181`
5. Démarrer les micro-services.

## Tests de bout en bout

1. Envoyer une requête POST à `http://localhost:8181/orders` avec le corps JSON suivant:

```json
{
  "customerId": "d215b5f8-0249-4dc5-89a3-51fd148cfb41",
  "restaurantId": "d215b5f8-0249-4dc5-89a3-51fd148cfb45",
  "price": 50.00,
  "items": [
    {
      "productId": "d215b5f8-0249-4dc5-89a3-51fd148cfb48",
      "quantity": 1,
      "price": "50.00",
      "subTotal": "50.00"
    }
  ],
  "address": {
    "street": "street_1",
    "postalCode": "1000AB",
    "city": "Amsterdam"
  }
}

```

## Réponse :

```json
{
  "orderTrackingId": "6a84f37a-6273-425e-9123-97c2a5ea7b84",
  "orderStatus": "PENDING",
  "message": "Order created successfully"
}
```
Le Web service retourne un token `orderTrackingId` pour suivre la commande. Dans votre cas, le token était `6a84f37a-6273-425e-9123-97c2a5ea7b84`.
Afin de suivre l'avancement de la commande, vous devez utiliser une requête GET comme suit :

```
GET http://localhost:8181/orders/6a84f37a-6273-425e-9123-97c2a5ea7b84
```
## Réponse :
```json
{
  "orderTrackingId": "534d0861-81b5-4e43-91b0-c98fa216d636",
  "orderStatus": "APPROVED",
  "failureMessages": []
}
```

