# Database Normalization

## Applicability
Having a normalized, relational, data model helps you understand the shape of your data.
I start out by modeling my data to at least third normal form, and maintaining that model in third normal form over time.
Once the data is in third normal form, I consider the performance requirements of each access pattern I need to support.
I then denormalize the data as needed, considering the tradeoff between performance and the additional complexity needed to account for the anomalies that denormalization make me vulnerable to.

For personal projects, I go so far as to maintain the applications' ability to switch between the less performant, normalized implementation, and the more complex, denormalized implementation.
I find it much easier to maintain my data models in the form of code.
I can get away with this because none of my personal projects are sensitive to market timing.

It takes human insight to normalise the data because normalization is dependent on semantics.
That is, you have to understand what the data means in order to normalise it.

## Not Normalized
- Suppliers are S1, S2, S3, S4
- Types of parts are P1, P2, P3, P4, P5, P6
- London has status 20
- Paris has status 10
- Suppliers S1 and S4 are in London
- Suppliers S2 and S3 are in Paris
- We have the following shipments
    - 300 of P1 from S1
    - 200 of P2 from S1
    - 400 of P3 from S1
    - 200 of P4 from S1
    - 100 of P5 from S1
    - 100 of P6 from S1
    - 300 of P1 from S2
    - 400 of P2 from S2
    - 200 of P2 from S3
    - 200 of P2 from S4
    - 300 of P4 from S4
    - 400 of P5 from S4

## First Normal Form (1NF)
DEFINITION: Every tuple contains exactly one value for each attribute

Shipments

Key: SUPPLIER, PART

| SUPPLIER | CITY   | STATUS | PART | QUANTITY |
|----------|--------|--------|------|----------|
| S1       | London | 20     | P1   | 300      |
| S1       | London | 20     | P2   | 200      |
| S1       | London | 20     | P3   | 400      |
| S1       | London | 20     | P4   | 200      |
| S1       | London | 20     | P5   | 100      |
| S1       | London | 20     | P6   | 100      |
| S2       | Paris  | 10     | P1   | 300      |
| S2       | Paris  | 10     | P2   | 400      |
| S3       | Paris  | 10     | P2   | 200      |
| S4       | London | 20     | P2   | 200      |
| S4       | London | 20     | P4   | 300      |
| S4       | London | 20     | P5   | 400      |

Anomalies
- INSERT: We can't express that S5 is in Athens
- DELETE: If we delete shipment S3 P2, we lose the information that S3 is in Paris
- UPDATE: If S1 moves from London to Amsterdam, we either have to update multiple places or risk inconsistency

## Second Normal Form (2NF)
DEFINITION: Is in first normal form, and each non-key attribute depends on the whole key

In the first normal form example, QUANTITY depends on both SUPPLIER and PART,
but CITY and STATUS only depend on SUPPLIER.
The solution is to separate CITY and STATUS from QUANTITY, as follows.

Supplier

Key: SUPPLIER

| SUPPLIER | CITY   | STATUS |
|----------|--------|--------|
| S1       | London | 20     |
| S2       | Paris  | 10     |
| S3       | Paris  | 10     |
| S4       | London | 20     |
| S5       | Athens | 30     |

Shipment

Key: SUPPLIER, PART

| SUPPLIER | PART | QUANTITY |
|----------|------|----------|
| S1       | P1   | 300      |
| S1       | P2   | 200      |
| S1       | P3   | 400      |
| S1       | P4   | 200      |
| S1       | P5   | 100      |
| S1       | P6   | 100      |
| S2       | P1   | 300      |
| S2       | P2   | 400      |
| S3       | P2   | 200      |
| S4       | P2   | 200      |
| S4       | P4   | 300      |
| S4       | P5   | 400      |

Anomalies
- INSERT: We can't express that Rome has status 50
- DELETE: If we delete S5, we lose the information that Athens has status 30
- UPDATE: If we change the status of London from 20 to 30, we either have to update multiple places or risk inconsistency

## Third Normal Form (3NF)
DEFINITION: Is in second normal form, and non-key attributes are mutually independent 

In the second normal form example, STATUS was dependent on CITY.
The solution is to separate STATUS as follows.

Supplier

Key: SUPPLIER

| SUPPLIER | CITY    |
|----------|---------|
| S1       | London  |
| S2       | Paris   |
| S3       | Paris   |
| S5       | Athens  |

City

Key: CITY

| CITY   | STATUS |
|--------|--------|
| London | 20     |
| Paris  | 10     |
| Athens | 30     |
| Rome   | 50     |

Shipment

Key: SUPPLIER, PART

| SUPPLIER | PART | QUANTITY |
|----------|------|----------|
| S1       | P1   | 300      |
| S1       | P2   | 200      |
| S1       | P3   | 400      |
| S1       | P4   | 200      |
| S1       | P5   | 100      |
| S1       | P6   | 100      |
| S2       | P1   | 300      |
| S2       | P2   | 400      |
| S3       | P2   | 200      |
| S4       | P2   | 200      |
| S4       | P4   | 300      |
| S4       | P5   | 400      |

## Other Normal Forms
Further normal forms become increasingly esoteric.
Third normal form is sufficient in most cases.

Other normal forms
- Elementary Key Normal Form (EKNF)
- Boyce-Codd Normal Form (BCNF)
- Fourth Normal From (4NF)
- Essential Tuple Normal Form (ETNF)
- Fifth Normal Form (5NF)
- Domain Key Normal Form (DKNF)
- Sixth Normal Form (6NF)

## References
- An Introduction to Database Systems, C.J. Date
- Temporal Data & the Relational Model, C.J. Date
