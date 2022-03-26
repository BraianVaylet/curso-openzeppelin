# 📝 Notas del Curso de OpenZepellin

## Clases

- Clase1: [Qué es OpenZeppelin](#Qué-es-OpenZeppelin)
- Clase2: [Propiedad con Ownable](#Propiedad-con-Ownable)
- Clase3: [Control de acceso basado en roles](#Control-de-acceso-basado-en-roles)
- Clase4: [Gestión de demoras con TimelockController](#Gestión-de-demoras-con-TimelockController)

## Contenido

### Qué es OpenZeppelin

#### ¿Qué es?

En el mundo del desarrollo de Smart Contract nos encontraremos con soluciones complejas, y es muy probable que no tengamos en cuenta el uso del gas o entremos en alguna vulnerabilidad al desarrollar el contrato.
Es por eso que existe Open Zeppelin:
Ofrece una serie de contratos ya desarrollados y auditados para mitigar estos problemas, como los son:

- Complejidad en las soluciones.
- Tiempo de desarrollo.
- Optimizacion en el uso del gas.
- Seguridad.

> Links:
>
> - [https://openzeppelin.com/](https://openzeppelin.com/)
> - [repo profe](https://github.com/platzi/curso-open-zeppelin/tree/main/contratos)

---

### Propiedad con Ownable

#### # Control de acceso: Gestión de permisos de ejecución

**Contrato Ownable:** Contrato que controla el acceso a las funciones para que ***solo su dueño (owner) pueda ejecutarlas.*** El owner puede ser modificad. (Un owner puede delegar su acceso a otra cuenta.)

#### # Código de ejemplo

```sol
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

import "@openzeppelin/contracts/access/Ownable.sol";

contract MiContrato is Ownable {
    // accesible por cualquier usuario
    function funcionAccesiblePorTodos() public {}

    // accesible solo por el owner
    function functionParaElOwner() public onlyOwner {}
}
```

> Links:
>
> - [repo OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts)
> - [repo OpenZeppelin Owner contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol)

---

### Control de acceso basado en roles

#### # Control de acceso: Restricción de accesos por roles

**Contrato AccessControl**: Es un contrato que permite definir diversos roles para restringir el acceso a las funciones de modo que se asignen a uno o mas roles.

#### # Código de ejemplo

```sol
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

import "@openzeppelin/contracts/access/AccessControl.sol";

contract ContratoConRoles is AccessControl {

    // Definimos roles. (La libreria usa bytes32, usamos keccak256 para hashear el string de forma sencilla.)
    bytes32 public constant ROL_ADMIN = keccak256("ROL_ADMIN");
    bytes32 public constant ROL_USUARIO = keccak256("ROL_USUARIO");

    // La funcion heredada _grantRole() permite agregar cuentas a roles y administrarlos.
    constructor() {
        _grantRole(ROL_ADMIN, msg.sender);
    }

    // La funcion heredada hasRole() valida roles.
    function soloAdmin() public  {
        require(hasRole(ROL_ADMIN, msg.sender), "Esta funcion solo puede ser utilizada por el ADMIN");
    }

    function soloUsuario() public  {
        require(hasRole(ROL_USUARIO, msg.sender), "Esta funcion solo puede ser utilizada por un USUARIO");
    }

    function agregarRol(bytes32 role, address account) public {
        require(hasRole(ROL_ADMIN, msg.sender), "Esta funcion solo puede ser utilizada por el ADMIN");
        _grantRole(role,account);
    }
}
```

> Links:
>
> - [repo OpenZeppelin AccessControl contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol)

---

### Gestión de demoras con TimelockController

**Contrato TimelockController**: Agrega una demora en la **ejecucion de una funcion de parte de un usuario con permisos**, para que en caso de que exista un conflicto haya tiempo de actuar.

##### Notas de un alumno

- al deployar el contrato se debe pasar arrays vacios para los proposers y los executers
- importante asignar al menos un proposer y un executor
- algunos de los parámetros son de tipo 32bytes, por lo que implica una conversion
- el argumento data es un calldata (callback), que se refiere al método a invocar lo que implica aplicarle un abiEncoding
- el delay es obligatorio y debe ser mayor o igual al minDelay
- el operationId se puede sacar invocando el hashOperation, aunque tambien se puede obtener de la respuesta
- la operation no se ejecuta sola al cumplirse el tiempo, sino que pasa a un estado Ready y entonces es que está disponible para que el Executor lo ejecute.
- Cuando lean la documentacion del TimelockController, lleguen hasta final donde explican los principales atributos y el ciclo de vida
- > [Ejemplo](https://github.com/vijoin/OpenZeppelin_Practices)

> Links:
>
> - [repo OpenZeppelin TimelockController contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/governance/TimelockController.sol)
