# Práctica 2: Refactoring

## Refactoring

### Razones para refactorizar

Posibles razones para refactorizar código, según [Code Complete]:

- Código duplicado
- Una función es demasiado larga
- Un bucle es demasiado largo o está demasiado anidado (demasiada complejidad)
- Una clase tiene poca cohesión
- Una interfaz no proporciona un nivel consistente de abstracción
- Una lista de parámetros tiene demasiados parámetros
- Los cambios dentro de una clase tienden a afectar a otras clases
- Las jerarquías de herencia tienen que ser modificadas en paralelo (afecta a todas las subclases un mismo cambio)
- Los atributos que se utilizan por varias clases no están organizados en una clase (se repiten)
- Una función utiliza más características propuestas por otra clase que de su propia clase
- Un tipo de datos primitivo esta sobrecargado
- Una clase no tiene una finalidad clara (no aporta mucho)
- Un objeto intermediario no hace nada
- Una función tiene un nombre que no especifica de forma clara su objetivo
- Los atributos de una clase son públicos
- Una subclase sólo utiliza un porcentaje mínimo de las funciones que hereda
- Se utilizan comentarios para explicar código díficil de entender
- Se usan variables globales
- Una función contiene código que parece que será necesario algún día pero en la actualidad no se utiliza


#### Refactorización en el uso de datos

- Reemplazar un número utilizado directamente por una constante
- Renombrar una variable para darle un nombre más claro o explicativo
- Convertir una variable que se usa en múltiples sitios en múltiples variables de único uso
- Usar una variable local para propósitos locales en lugar de un parámetro de una función
- Convertir el uso de datos primitivos en el uso de una clase
- Convertir un array (de bajo nivel) en un objeto
- Encapsular una colección

#### Refactorización en la implementación de sentencias/instrucciones

- Descomponer una expresión lógica
- Convertir una expresión lógica compleja en una función lógica con un nombre correcto para su definición
- Unificar fragmentos de código duplicado en diferentes partes de una expresión condicional
- Eliminar el uso de `break` o `return` en lugar del uso de variables de control en los bucles
- Devolver lo más rápido posible la solución en lugar de asignar el valor a una variable dentro de instrucciones `if`-`else`
- Reemplazar condicionales (especialmente las sentencias `case`) por el uso de polimorfismo

#### Refactorización en la implementación de funciones

- Extraer una función desde código que se repite en varios lugares
- Convertir una función demasiado larga en una clase
- Sustituir un algoritmo complejo por uno simple
- Combinar funciones similares en una única
- Pasar el objeto completo en lugar de seleccionar algunos campos específicos (en el caso de que sean muchos)
- Pasar algunos campos específicos en lugar del objeto completo (en el caso de que sean pocos campos los utilizados).

#### Refactorización en la implementación de clases/interfaces

- Extraer código especializado en subclases
- Combinar código similar en superclases
- Mover una función a otra clase en la que tenga más coherencia
- Convertir una clase demasiado larga en dos
- Eliminar una clase sin utilidad
- Reemplazar herencia por composición (en el caso de que sea necesario)
- Reemplazar composición por herencia (en el caso de que sea necesario)
- Unificar una superclase y una subclase si su implementación es muy similar.

#### Refactorización a nivel de sistema

- Cambiar la asociación unidireccional de clases a bidireccional (en caso de que sea necesario)
- Cambiar la asociación bidireccional de clases a unidireccional (en caso de que sea necesario)
- Proveer de una factoria para crear los objetos en lugar de usar un constructor simple
- Reemplazar los códigos de error con excepciones (en caso de que sea necesario).

## Data streams

Un data stream representa una secuencia de elementos que soportan diferentes tipos de operaciones para realizar cálculos sobre ellos

### Operaciones

Las operaciones sobre un stream pueden ser intermediarias o terminales

  - Las operaciones __intermediarias__ devuelven un nuevo stream permitiendo encadenar múltiples operaciones intermediarias sin usar punto y coma
  - Las operaciones __terminales__ son nulas o devuelven un resultado de un tipo diferente, normalmente un valor agregado a partir de cómputos anteriores

---

### Ejemplo v0.1

```java
public class Main{
  public static void main(String []args){      
    List<String> myList =
      Arrays.asList("a1", "a2", "b1", "c2", "c1");
    myList
      .stream()
      .filter(s -> s.startsWith("c"))
      .map(String::toUpperCase)
      .sorted()
      .forEach(System.out::println);      
  }
}
```

---

### Interfaces funcionales

- Las operaciones que se aplican sobre un _stream_ aceptan algún parámetro en forma de interfaz funcional o expresión lambda

  - Una __interfaz funcional__ es un objeto cuyo tipo (clase) representa a una función ejecutable con un cierto número de parámetros (normalmente 0, 1 o 2)
  - Una __expresión lambda__ es una interfaz funcional anónima, que especifica el comportamiento de la operación, pero sin especificar formalmente su nombre y tipo de parámetros

- Las operaciones aplicadas no pueden modificar el _estado_ del stream original 

---

En el ejemplo anterior, se puede observar que:

- `filter`, `map` y `sorted` son operaciones intermediarias
- `forEach` es una operación terminal
- Ninguna de las operaciones modifica el estado de `myList` añadiendo o eliminando elementos
- Sólo se filtran ciertos elementos, se transforman a mayúsculas, se ordenan (por defecto, alfabéticamente) y se imprimen por pantalla

---

### Ejemplo v0.2

```java
    List<String> myList =
      Arrays.asList("a1", "a2", "b1", "c2", "c1");

    myList
      .stream()
      .filter(s -> s.startsWith("c"))
      .map(String::toUpperCase)
      .sorted()
      .forEach(System.out::println);
    
    myList
      .stream()
      .reduce( (a,b) -> a + " " + b )
      .ifPresent(System.out::println);
```

---

### Más información

- Winterbe: [Java 8 stream tutorial](https://winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples/)
- Oracle: [Procesamiento de datos con streams de Java](https://www.oracle.com/lad/technical-resources/articles/java/processing-streams-java-se8.html) 
- Oracle: [Introducción a Expresiones Lambda y API Stream en Java](https://www.oracle.com/lad/technical-resources/articles/java/expresiones-lambda-api-stream-java-part2.html)

## Ejercicios propuestos

### Ejercicio 1

Dado los siguientes fragmentos de código, responder a las siguientes preguntas:

#### `GroupOfUsers.java`

```java
public class GroupOfUsers {
 
    private static Map<String, Integer> usersWithPoints =
      new HashMap<String, Integer>() {{
        put("User1", 800);
        put("User2", 550);
        put("User3", 20);
        put("User4", 300);
    }};
    
    public List<String> getUsers() {
        List<String> users = new ArrayList<String>();
        
        //Sorting users by points
        usersWithPoints.entrySet()
        .stream()
        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
        .forEachOrdered(x -> users.add(x.getKey()));
        
        //Capitalizing the names of the users
        List<String> usersCapitalized = new ArrayList<String>();
        users.forEach(x -> usersCapitalized.add(x.toUpperCase()));
        
        return usersCapitalized;
 }

}
```

#### `Main.java`

```java
    ...
    GroupOfUsers group = new GroupOfUsers();
    List<String> users = group.getUsers();
    System.out.println("The users are: " + users);
    ...
```

#### Preguntas propuestas

En la siguiente lista se incluyen 10 posibles problemas que pueden encontrarse en el código de la implementación anterior:

- Código duplicado 
- Funciones con nombre que no especifica de forma clara su objetivo
- Rutinas demasiado largas
- Bucles demasiado largos o demasiado anidados
- Funciones con demasiada responsabilidad (no tienen asignada una única responsabilidad u operación a resolver)
- Lista de parámetros con demasiados parámetros
- Los cambios de una clase tienden a afectar a otras
- Se utilizan comentarios para explicar código díficil de entender
- Se usan variables globales
- Los cambios dentro de una clase tienden a afectar a otras clases

a) ¿Existe algún tipo de problema en la implementación anterior de los que se incluye en la lista anterior? ¿Es necesario aplicar refactoring en este caso? En el caso de que existan problemas, indique cuáles son y qué tipos de problemas piensa que generarían en el futuro si no se aplica el refactoring ahora.


Si existen algunos problemas de la lista. Son los siguientes:

    - **Funciones con nombre que no especifican claramente su objetivo**: Los nombres de la función `getUsers()` no es lo suficientemente descriptivo para entender su propósito sin leer su implementación.
    - **Rutinas demasiado largas**: `getUsers()` realiza varias operaciones en secuencia, lo que la hace difícil de entender y mantener a medida que crece en complejidad.
    - **Funciones con demasiada responsabilidad**: `getUsers()` realiza tanto la clasificación de usuarios por puntos como la capitalización de los nombres de los usuarios.
    - **Variables globales**: El map `usersWithPoints` se declara como una variable estática y privada en la clase.

Si es necesario aplicar refactoring ya que los problemas podrían generar las siguientes dificultades en el futuro:

    * Legibilidad y mantenibilidad reducidas: El código se volverá más difícil de entender y modificar a medida que crezca en tamaño y complejidad.
    * Mayor riesgo de introducir errores: La falta de nombres descriptivos y funciones con responsabilidades claras dificulta la detección de errores y el seguimiento de los problemas en el código.
    * Dificultad para realizar cambios: Los cambios futuros pueden requerir modificaciones en múltiples partes del código debido a la falta de modularidad y responsabilidad única.


Por tanto, aplicar refactoring puede mejorar la calidad del código, facilitar su comprensión, reducir el riesgo de errores y aumentar la mantenibilidad a largo plazo.


b) En el caso de que la implementación necesite la aplicación de refactoring, realice los cambios oportunos e indique las mejoras que aporta su implementación respecto a la original.

Una aplicación de refactoring solucionando los problemas anteriores sería la siguiente:

```java
public class GroupOfUsers {
 
    private Map<String, Integer> usersWithPoints;
    
    public GroupOfUsers() {
        usersWithPoints = new HashMap<>();
        initializeUsers();
    }
    
    private void initializeUsers() {
        usersWithPoints.put("User1", 800);
        usersWithPoints.put("User2", 550);
        usersWithPoints.put("User3", 20);
        usersWithPoints.put("User4", 300);
    }
    
    public List<String> getUsersInDescendingOrder() {
        List<String> users = sortUsersByPoints();
        return capitalizeUserNames(users);
    }
    
    private List<String> sortUsersByPoints() {
        List<Map.Entry<String, Integer>> entries = new ArrayList<>(usersWithPoints.entrySet());
        entries.sort(Map.Entry.comparingByValue(Comparator.reverseOrder()));
        
        List<String> sortedUsers = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : entries) {
            sortedUsers.add(entry.getKey());
        }
        
        return sortedUsers;
    }
    
    private List<String> capitalizeUserNames(List<String> users) {
        List<String> capitalizedUsers = new ArrayList<>();
        for (String user : users) {
            capitalizedUsers.add(user.toUpperCase());
        }
        
        return capitalizedUsers;
    }
}
```

Las mejoras implementadas en este código son:

    1. Ya no está la variable local, se ha creado un método para inicializarlo aportando claridad y mantenibilidad al código.
    2. Se ha cambiado el nombre al método `getUsers()`, dejando más claro para que es el método.
    3. He separado en métodos privados `sortUsersByPoints()` y `capitalizeUserNames()` lo que divide las responsabilidades y mejora la legibilidad y mantenibilidad del código.
    4. He cambiado los búcles `forEach` por bucles `for` en los métodos mencionados en el apartado 3, para así mantener la compatibilidad de versiones.


### Ejercicio 2

Dado los siguientes fragmentos de código, responder a las siguientes preguntas:

#### `GroupOfUsers.java`

```java
public class GroupOfUsers {
    
    private static Map<String, Integer> usersWithPoints_Group1 =
      new HashMap<String, Integer>() {{
        put("User1", 800);
        put("User2", 550);
        put("User3", 20);
        put("User4", 300);
    }};
    
    private static Map<String, Integer> usersWithPoints_Group2 =
      new HashMap<String, Integer>() {{
        put("User1", 10);
        put("User2", 990);
        put("User3", 760);
        put("User4", 230);
    }};
    
    private static Map<String, Integer> usersWithPoints_Group3 =
      new HashMap<String, Integer>() {{
        put("User1", 1000);
        put("User2", 200);
        put("User3", 5);
        put("User4", 780);
    }};
    
    public List<ArrayList<String>> getUsers() {
        List<String> users1 = new ArrayList<String>();
        List<String> users2 = new ArrayList<String>();
        List<String> users3 = new ArrayList<String>();
        List<ArrayList<String>> users = new ArrayList<ArrayList<String>>();
        
        //Sorting users by points
        usersWithPoints_Group1.entrySet()
        .stream()
        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
        .forEachOrdered(x -> users1.add(x.getKey()));
        
        usersWithPoints_Group2.entrySet()
        .stream()
        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
        .forEachOrdered(x -> users2.add(x.getKey()));
        
        usersWithPoints_Group3.entrySet()
        .stream()
        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
        .forEachOrdered(x -> users3.add(x.getKey()));
        
        //Capitalizing the names of the users
        List<String> usersCapitalized1 = new ArrayList<String>();
        List<String> usersCapitalized2 = new ArrayList<String>();
        List<String> usersCapitalized3 = new ArrayList<String>();
        
        users1.forEach(x -> usersCapitalized1.add(x.toUpperCase()));
        users2.forEach(x -> usersCapitalized2.add(x.toUpperCase()));
        users3.forEach(x -> usersCapitalized3.add(x.toUpperCase()));
        
        //Adding users to the main list
        users.add((ArrayList<String>)usersCapitalized1);
        users.add((ArrayList<String>)usersCapitalized2);
        users.add((ArrayList<String>)usersCapitalized3);
        
        return users;
    }

}
```

#### `Main.java`

```java
    ...
    GroupOfUsers group = new GroupOfUsers();
    List<ArrayList<String>> users = group.getUsers();
    System.out.println("The users are: " + users);
    ...
```

#### Preguntas propuestas

Responda a las siguientes cuestiones, teniendo en cuenta la lista de los 10 posibles problemas del ejercicio anterior

a) El software del ejercicio anterior ha evolucionado añadiendo nueva funcionalidad en su implementación. ¿Existe algún tipo de problema en esta versión de la implementación de los que se incluyen en la lista? ¿Es necesario aplicar refactoring en este caso? En el caso de que existan problemas, indique cuáles son y qué tipos de problemas piensa que generarían en el futuro si no se aplica el refactoring ahora.


Presenta los mismo problemas que el ejercicio anterior, con la diferencia de que aquí hay mucho más código, porque realiza lo mismo varías veces. Esto muestra que los problemas futuros escritos en el apartado anterior son ciertos y se dan en este código.


b) En el caso de que la implementación necesite la aplicación de refactoring, realice los cambios oportunos e indique las mejoras que aporta su implementación respecto a la original.


Mi aplicación del refactoring sería:

```java
import java.util.*;

public class GroupOfUsers {
    
    private static Map<String, Integer> usersWithPoints_Group1 = createGroup1();
    private static Map<String, Integer> usersWithPoints_Group2 = createGroup2();
    private static Map<String, Integer> usersWithPoints_Group3 = createGroup3();
    
    private static Map<String, Integer> createGroup1() {
        Map<String, Integer> group1 = new HashMap<>();
        group1.put("User1", 800);
        group1.put("User2", 550);
        group1.put("User3", 20);
        group1.put("User4", 300);
        return group1;
    }
    
    private static Map<String, Integer> createGroup2() {
        Map<String, Integer> group2 = new HashMap<>();
        group2.put("User1", 10);
        group2.put("User2", 990);
        group2.put("User3", 760);
        group2.put("User4", 230);
        return group2;
    }
    
    private static Map<String, Integer> createGroup3() {
        Map<String, Integer> group3 = new HashMap<>();
        group3.put("User1", 1000);
        group3.put("User2", 200);
        group3.put("User3", 5);
        group3.put("User4", 780);
        return group3;
    }
    
    public List<List<String>> getUsers() {
        List<Map.Entry<String, Integer>> sortedGroup1 = sortUsersByPoints(usersWithPoints_Group1);
        List<Map.Entry<String, Integer>> sortedGroup2 = sortUsersByPoints(usersWithPoints_Group2);
        List<Map.Entry<String, Integer>> sortedGroup3 = sortUsersByPoints(usersWithPoints_Group3);
        
        List<List<String>> users = new ArrayList<>();
        users.add(capitalizeUserNames(sortedGroup1));
        users.add(capitalizeUserNames(sortedGroup2));
        users.add(capitalizeUserNames(sortedGroup3));
        
        return users;
    }
    
    private List<Map.Entry<String, Integer>> sortUsersByPoints(Map<String, Integer> usersWithPoints) {
        List<Map.Entry<String, Integer>> sortedUsers = new ArrayList<>(usersWithPoints.entrySet());
        sortedUsers.sort(Map.Entry.comparingByValue(Comparator.reverseOrder()));
        return sortedUsers;
    }
    
    private List<String> capitalizeUserNames(List<Map.Entry<String, Integer>> users) {
        List<String> capitalizedUsers = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : users) {
            capitalizedUsers.add(entry.getKey().toUpperCase());
        }
        return capitalizedUsers;
    }
}
```

Los cambios realizados son:
    1. Se crearon métodos separados `createGroup1()`, `createGroup2()`, `createGroup3()` para inicializar los grupos de usuarios y sus puntos para así mejorar la legibilidad del código y hacerlo más modular.
    2. Se creó un método `sortUsersByPoints()` para ordenar los usuarios según sus puntos y así evitar la duplicación de código, además mejora la reutilización.
    3. Se creó un método `capitalizeUserNames()` para capitalizar los nombres de usuario y así separar la responsabilidad y mejorar la legibilidad.
    4. Se utilizaron tipos de datos más genéricos en lugar de tipos específicos para las listas, lo que aporta mayor flexibilidad.


## Referencias

[Code Complete]: https://www.amazon.es/Code-Complete-Practical-Costruction-Professional/dp/0735619670
\[1\] [Code Complete: A Practical Handbook of Software Construction, Second Edition.][Code Complete]
