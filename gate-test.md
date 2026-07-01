# Gate prueba de bloqueo 

## Prueba de bloqueo

Para comenzar con la prueba se va a agregar una historia de usuario no testeable, sin criterios de aceptación, demasiado grande y con dependencias inexistentes en el archivo backlog.json.

![alt text](./img/story.png)

Se ejecuta el comando /delivery:generate-stories

![alt text](./img/command1.png)

El modelo detecta e indica que la historia US-08 es inválida, indicando sus fallas.

![alt text](./img/errors1.png)

Al final el modelo decide no continuar los procesos relacionados con la historia de usuario que tenía conflictos y decide eliminarla del proceso por inválida.

![alt text](./img/end1.png)


## Prueba luego de corregir la historia

Para realizar la prueba se agregó una historia de usuario que cumpla con las validaciones que no pudo cumplir la historia anterior.

![alt text](./img/story2.png)

Se ejecuta el comando /delivery:generate-stories

![alt text](./img/command2.png)

Esta vez, el modelo acepta valida todas las historias de usuario y continua con los procesos posteriores para generar las salidas.

![alt text](./img/end2.png)

![alt text](./img/result2.png)