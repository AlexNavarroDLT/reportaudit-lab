# Laboratorio 1 — Bitácora de auditoría de la cadena de suministro

- **Autor/a:** Alex Navarro de la Torre 
- **Repositorio:**https://github.com/AlexNavarroDLT/reportaudit-lab.git
- **Sistema operativo y versión de Python usados:**
Windows

> Completa cada sección en el momento en que la guía te lo pide, no al final.
> Una bitácora escrita "de memoria" al terminar no sirve como evidencia.

---

## Parte B — Auditoría manual (antes de usar ninguna herramienta)

| # | Función | Línea | Qué sospechas | Dato de entrada (*source*) | Destino peligroso (*sink*) |
|---|---|---|---|---|---|
| 1 | buscar_reportes_cliente | 42 | Posible inyección SQL por concatenar el nombre del cliente directamente en la consulta. El nombre o'brien_ltd provoca un error de sintaxis SQL y una respuesta HTTP 500. | Parámetro cliente de la URL, recibido como nombre_cliente. | 
cursor.execute(query) |

| 2 | convertir_a_pdf | 51 | Posible inyección de comandos al concatenar un nombre de archivo controlado por el usuario en una orden ejecutada por la shell. | Parámetro archivo de la URL, recibido como nombre_archivo. | os.system(comando) |

| 3 | cargar_configuracion | 33 | Carga insegura de YAML: utiliza un cargador que permite construir objetos de Python y podría ejecutar código si recibe contenido malicioso. | Contenido del archivo config.yaml. | yaml.load(f, Loader=yaml.Loader). |
| 4 | hash_password_legacy | 57 | Uso de MD5 sin sal para contraseñas. Es un algoritmo rápido que facilita probar muchas contraseñas candidatas y produce el mismo hash para contraseñas iguales. | Parámetro password de la función | hashlib.md5(password.encode()). |
| 5 | constantes NOTIFICATION_API_KEY y SMTP_PASSWORD. | 21,22 | Credenciales escritas directamente en el código y expuestas a quien tenga acceso al repositorio. | Valores de las credenciales incluidos en el código | Archivo y repositorio donde quedan expuestas |

**Impacto en el negocio:** para cada sospecha, explica en una frase qué
consecuencia tendría para ReportAudit y sus clientes si fuera real (qué datos,
qué sistema o qué credencial quedarían expuestos).

|1| Un usuario podría manipular la consulta y acceder a reportes de otros clientes. Además, un nombre legítimo con apóstrofo provoca un fallo del servicio.

|2| Un usuario podría ejecutar comandos con los permisos del servicio y acceder, modificar o eliminar archivos, incluidos los reportes de los clientes.

|3| El comentario explica que se añadió yaml.Loader para resolver un error tras actualizar PyYAML, pero el cargador elegido permite interpretar contenido potencialmente peligroso.
Si un atacante pudiera modificar el archivo de configuración, podría ejecutar código con los permisos del servicio al cargarse el YAML, comprometiendo los reportes y las credenciales accesibles.

|4| Si se obtuvieran hashes de contraseñas generados por esta función, sería más fácil adivinar las contraseñas y acceder sin autorización a las cuentas que las utilicen.

|5| Si las credenciales fueran válidas, alguien con acceso al código podría utilizarlas para acceder a los servicios asociados o enviar mensajes suplantando a ReportAudit.
---

## Matriz de detección (se completa a lo largo del laboratorio)

Marca ✓ (lo detectó, anota la regla) o ✗ (no lo detectó) en cada columna cuando
llegues a la parte correspondiente.

| Hallazgo | Manual (B) | SonarQube for IDE sin conexión (D) | SonarQube for IDE en Connected Mode (E) | SonarQube Cloud (F) | CodeQL (F) | Semgrep (G) | Trivy (K) |
|---|---|---|---|---|---|---|---|
| H1 Inyección SQL en `buscar_reportes_cliente` | ✓ | ✗ | ✗ | | | | n/a |
| H2 Inyección de comandos en `convertir_a_pdf` | ✓ | ✗ | ✗ | | | | n/a |
| H3 Deserialización YAML insegura en `cargar_configuracion` | ✓ | ✗ | ✗ | | | | n/a |
| H4 Hash MD5 en `hash_password_legacy` | ✓ | ✓ | ✓ | | | | n/a |
| H5 Clave de API escrita en el código | ✓ | ✗ | ✗ | | | | |
| H6 Contraseña SMTP escrita en el código | ✓ | ✓ | ✓ | | | | |
**Conclusión de la matriz** (Parte K): ¿alguna herramienta lo detectó todo? ¿Qué
te dice eso sobre depender de una sola herramienta?

---

## Parte J — SBOM: el iceberg medido

| Dato | Valor |
|---|---|
| Dependencias directas (`requirements.in`) |  |
| Componentes Python en el SBOM |  |
| Otros componentes que aparezcan en el SBOM (si los hay) y de dónde salen |  |
| Formato y versión de especificación del SBOM (`bomFormat`, `specVersion`) |  |

---

## Parte J — Triage de vulnerabilidades de dependencias (Grype)

| Paquete | Versión | ¿Directa o transitiva? (usa `# via`) | CVE / GHSA | Severidad | Corregida en | ¿Explotable en ReportAudit? ¿Por qué? | Decisión |
|---|---|---|---|---|---|---|---|
|  |  |  |  |  |  |  |  |

**Comparación con Dependabot** (Parte H): ¿las alertas coinciden con Grype? Explica
cualquier diferencia.

**Documento VEX:** copia `plantillas/reportaudit.openvex.json` a
`docs/evidencias/`, rellénalo, enlázalo aquí y resume en una frase la
justificación.

---

## Parte L y M — Antes y después

| Medida | Antes | Después |
|---|---|---|
| Hallazgos de Semgrep en `app/` |  |  |
| Alertas abiertas de CodeQL (Security → Code scanning) |  |  |
| Vulnerabilidades en SonarQube Cloud (rama main) |  |  |
| Security Hotspots por revisar en SonarQube Cloud |  |  |
| Vulnerabilidades de Grype sobre el SBOM |  |  |
| Alertas abiertas de Dependabot |  |  |

---

## Preguntas de comprobación (Sección 7 de la guía)

1.
2.
3.
4.
5.
6.
7.
8.
9.
10.
11.
12.
