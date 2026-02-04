# 🧪 PentestLab: Operación Legacy Vault

Escenario: Black Box – Docker, Redes & Explotación Web
Nivel: Intermedio
Rol: Red Team

⚠️ Disclaimer Ético & Legal

Este laboratorio tiene fines exclusivamente educativos.
Cualquier prueba de penetración realizada fuera de un entorno controlado y sin autorización explícita por escrito constituye un delito.
Todas las actividades descritas en este documento se ejecutan únicamente dentro del entorno Docker aislado provisto en esta práctica.

📌 Objetivo del Laboratorio

Simular un ataque realista de Red Team contra una aplicación web vulnerable, identificando debilidades en red y aplicación, y posteriormente proponer medidas de mitigación (Blue Team) para fortalecer la seguridad del sistema.

.

🛠️ Metodología (Paso a Paso)

🔹 Fase 1: Despliegue del Entorno
Herramientas:
Docker
Docker Compose
Procedimiento:
Se crea un archivo docker-compose.yml que levanta:
Una máquina víctima: Mutillidae (OWASP vulnerable app).
Una máquina atacante: Parrot Security OS.
Ambos contenedores se comunican dentro de una red bridge aislada (pentest-net).
El servicio vulnerable se expone localmente en el puerto 8080.

🔹 Fase 2: Sniffing de Red (Análisis de Tráfico)
Herramientas:
tcpdump (contenedor atacante)
Wireshark (host Windows)
Procedimiento:
Se ejecuta tcpdump dentro del contenedor atacante.
El tráfico se envía en tiempo real a Wireshark mediante una tubería (pipe).
Se filtran paquetes HTTP POST para inspeccionar credenciales.
Resultado:
Captura de credenciales enviadas en texto plano a través de HTTP.

🔹 Fase 3: Reconocimiento de Red
Herramientas:
Nmap
Procedimiento:
Se realiza un Ping Sweep para identificar hosts activos.
Se ejecuta un escaneo completo de puertos y servicios (-sV -sC -p-).
Resultado:
Identificación de servicios web expuestos y versiones vulnerables.

🔹 Fase 4: Explotación Web (SQL Injection)
Herramientas:
SQLMap
Procedimiento:
Se identifica un parámetro vulnerable en la página User Info.
SQLMap automatiza la inyección SQL.
Se enumeran bases de datos, tablas y credenciales.
Resultado:
Acceso no autorizado a la base de datos nowasp y extracción de cuentas.

🔹 Fase 5: Fuerza Bruta de Credenciales
Herramientas:
Hydra
Wordlist RockYou
Procedimiento:
Se ataca el formulario de login.
Se prueba un diccionario de contraseñas.
Se detecta el mensaje de error para identificar intentos fallidos.
Resultado:
Compromiso de cuentas mediante contraseñas débiles.

🔹 Fase 6: Cracking de Hashes
Herramientas:
John The Ripper
Procedimiento:
Se guarda el hash obtenido desde la base de datos.
Se ejecuta un ataque de diccionario.
Resultado:
Recuperación de contraseñas almacenadas con hash inseguro (MD5).

Matriz de hallazgos
| # | Vulnerabilidad                  | Descripción                            | Severidad  |
| - | ------------------------------- | -------------------------------------- | ---------- |
| 1 | Tráfico HTTP sin cifrar         | Credenciales capturadas en texto plano | 🔴 Alta    |
| 2 | SQL Injection                   | Manipulación de consultas SQL          | 🔴 Crítica |
| 3 | Contraseñas débiles             | Uso de passwords comunes               | 🟠 Media   |
| 4 | Hash inseguro (MD5)             | Algoritmo obsoleto                     | 🟠 Media   |
| 5 | Falta de rate limiting          | Permite fuerza bruta                   | 🟠 Media   |
| 6 | Falta de validación de entradas | Inputs sin sanitizar                   | 🔴 Alta    |

🛡️ Sección Blue Team
🔐 Recomendaciones de Mitigación

1️⃣ Protección del Tráfico
Implementar HTTPS (TLS) para cifrar comunicaciones.
Redirigir automáticamente HTTP → HTTPS.

2️⃣ Prevención de SQL Injection
Usar Prepared Statements / Queries Parametrizadas.
Evitar concatenación directa de consultas SQL.
Implementar ORM seguros.

3️⃣ Gestión Segura de Contraseñas
Aplicar hashing fuerte: bcrypt, Argon2 o PBKDF2.
Usar salt único por usuario.

4️⃣ Defensa contra Fuerza Bruta
Implementar rate limiting.
Bloqueo temporal tras múltiples intentos fallidos.
Uso de CAPTCHA.

5️⃣ Validación de Entradas
Validar y sanitizar todos los inputs del usuario.
Implementar listas blancas (allowlists).

6️⃣ Monitoreo y Detección
Registrar intentos fallidos de autenticación.
Usar IDS/IPS para detectar patrones anómalos.
Revisar logs periódicamente.

✅ Conclusión

Este laboratorio demuestra cómo una aplicación mal configurada puede ser comprometida en múltiples fases de la Kill Chain, desde reconocimiento hasta post-explotación.
La correcta aplicación de controles defensivos reduce significativamente la superficie de ataque y fortalece la postura de seguridad del sistema.

Resultado:
Entorno de pruebas completamente funcional y aislado.
