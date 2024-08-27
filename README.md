# Implementación de una Suite de Pruebas Unitarias en un Ciclo de Integración Continua utilizando JUnit y Jenkins

## Tabla de Contenidos

1. [Introducción](#1-introducción)
2. [Requisitos Previos](#2-requisitos-previos)
3. [Creación de Pruebas Unitarias](#3-creación-de-pruebas-unitarias)
4. [Creación del Pipeline de Jenkins](#4-creación-del-pipeline-de-jenkins)
5. [Resultados del Testing](#5-resultados-del-testing)
6. [Configuración de Informes de Cobertura de Código](#6-configuración-de-informes-de-cobertura-de-código)
7. [Configuración de Notificaciones](#7-configuración-de-notificaciones)
8. [Conclusión](#8-conclusión)
9. [Documentación](#9-documentación)


## 1. Introducción
El objetivo de esta documentación es detallar el proceso de implementación de una suite de pruebas unitarias en un ciclo de integración continua utilizando JUnit y Jenkins. Este proceso asegura que el código se prueba de manera automática y continua para detectar errores lo antes posible

## 2. Requisitos Previos
### Herramientas
- ![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)
- ![Apache Maven](https://img.shields.io/badge/Apache%20Maven-C71A36?style=for-the-badge&logo=Apache%20Maven&logoColor=white)
- ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
- **JUnit 5**
- **JaCoCo**

## 3. Creación de Pruebas Unitarias
### Ejemplo de Prueba
```java
package com.example;

import static org.junit.jupiter.api.Assertions.assertEquals;
import org.junit.jupiter.api.Test;

public class CalculatorTest {

    @Test
    public void testAdd() {
        Calculator calculator = new Calculator();
        assertEquals(5, calculator.add(2, 3));
    }
}
```
## 4. Creación del Pipeline de Jenkins
```java
pipeline {
    agent any

    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vngerus/Maven-JUnite.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Code Coverage') {
            steps {
                bat 'mvn jacoco:report'
            }
        }
    }

    post {
        always {
            slackSend (message: "Jenkins Build ${currentBuild.fullDisplayName} Estado actual:")
        }
        success {
            slackSend (message: "Jenkins Build ${currentBuild.fullDisplayName} Se ha completado exitosamente")
        }
        failure {
            slackSend (message: "Jenkins Build ${currentBuild.fullDisplayName} Ha fallado la implementación")
        }
    }
}
```

## 5. Resultados del testing:
com.example.AppTest.txt 
```java
-------------------------------------------------------------------------------
Test set: com.example.AppTest
-------------------------------------------------------------------------------
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.016 s - in com.example.AppTest
```
com.example.CalculatorTest
```java
-------------------------------------------------------------------------------
Test set: com.example.CalculatorTest
-------------------------------------------------------------------------------
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.001 s - in com.example.CalculatorTest
```
## 6. Configuración de informes de Cobertura de Código:
-  JaCoCo: Se utiliza debido a su compatibilidad con las versiones más recientes de Java, incluido JDK 17. Cobertura, por otro lado, no es compatible con JDK 9 en adelante, lo que limita su uso en proyectos modernos.
-  Configuración del Plugin de JaCoCo:
  ```xml
  <build>
    <plugins>
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.8</version>
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>test</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
  ```
## 7. Configuración de Notificaciones
 ### Slack como preferencia 
 ![Jenkins](https://github.com/vngerus/Maven-JUnite/blob/main/img/jenkins.png)

## 8. Conclusión:
Al seguir implementando estas mejoras y expandiendo las capacidades del pipeline de CI/CD, el equipo de desarrollo puede aumentar la calidad, seguridad y eficiencia del ciclo de vida del software, logrando un proceso de entrega más robusto y confiable.


## 9. Documentación

Para obtener más información sobre las herramientas y tecnologías utilizadas en este proyecto, consulta los siguientes recursos:

- **[Jenkins Documentation](https://www.jenkins.io/doc/)**: Guía oficial para configurar y utilizar Jenkins.
- **[JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)**: Documentación oficial de JUnit 5, incluyendo guías sobre pruebas unitarias y ejemplos.
- **[Jenkins Pipeline](https://www.jenkins.io/doc/book/pipeline/)**: Documentación completa sobre cómo crear y administrar pipelines en Jenkins.
- **[JaCoCo Documentation](https://www.jacoco.org/jacoco/trunk/doc/)**: Manual de usuario para JaCoCo, que cubre cómo configurar y generar informes de cobertura de código.
