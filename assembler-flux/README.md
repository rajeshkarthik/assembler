# assembler-flux

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/io.github.pellse/assembler-flux/badge.svg)](https://maven-badges.herokuapp.com/maven-central/io.github.pellse/assembler-flux)

## Usage Example

```java
Flux<Transaction> transactionFlux = assemblerOf(Transaction.class)
    .fromSupplier(this::getCustomers, Customer::getCustomerId)
    .assembleWith(
        oneToOne(this::getBillingInfoForCustomers, BillingInfo::getCustomerId),
        oneToManyAsList(this::getAllOrdersForCustomers, OrderItem::getCustomerId),
        Transaction::new)
    .using(fluxAdapter(elastic()))
```
or
```java
Flux<Transaction> transactionFlux = Flux.fromIterable(getCustomers()) // or just getCustomerFlux()
    .buffer(10) // or bufferTimeout(10, ofSeconds(5)) to e.g. batch every 5 seconds or max of 10 customers
    .flatMap(customers -> assemblerOf(Transaction.class)
        .from(customers, Customer::getCustomerId)
        .assembleWith(
            oneToOne(this::getBillingInfoForCustomers, BillingInfo::getCustomerId),
            oneToManyAsList(this::getAllOrdersForCustomers, OrderItem::getCustomerId),
            Transaction::new)
        .using(fluxAdapter())) // parallel scheduler used by default
```
