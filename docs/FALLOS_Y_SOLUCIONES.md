```markdown
# 🐞 Fallos encontrados y sus soluciones

Durante la práctica se identificaron varios errores. Aquí se documentan y se explica cómo se resolvieron.

---

## 1️⃣ Error en la fecha del comando `tar`

**Descripción:**  
Se ejecutó:
```bash
sudo tar -czf ~/backups/diarios/completo_$(date 20260306).tar.gz /etc/ssh/ /etc/apache2/
El sistema respondió: date: invalid date ‘20260306’.

Causa:
La sintaxis $(date 20260306) es incorrecta. date espera un formato de salida, no un valor fijo.

Solución:
Usar $(date +%Y%m%d) para obtener la fecha actual en formato YYYYMMDD.

bash
sudo tar -czf ~/backups/diarios/completo_$(date +%Y%m%d).tar.gz /etc/ssh /etc/apache2
Además, se quitó la barra final en los directorios (opcional, pero más limpio).

2️⃣ Error al verificar integridad por nombre duplicado
Descripción:
Al hacer tar -tzf ~/backups/diarios/completo_.tar.gz el sistema no encontraba el archivo porque el nombre real era completo_20260311.tar.gz.

Causa:
El comando se ejecutó con un nombre genérico sin la fecha.

Solución:
Usar ls para listar los archivos y copiar el nombre exacto, o usar tabulador para autocompletar.

bash
ls ~/backups/diarios/
tar -tzf ~/backups/diarios/completo_20260311.tar.gz | head -10
3️⃣ Permisos denegados al escribir en /var/log
Descripción:
El script backup_enterprise.sh intentaba escribir en /var/log/backup_enterprise.log sin sudo, dando error de permisos.

Causa:
El directorio /var/log pertenece a root y solo root puede escribir.

Solución:
Ejecutar el script con sudo, o cambiar la ubicación del log a un directorio con permisos de usuario (ej. $HOME/logs/backup_enterprise.log). En el script final se usó tee -a "$LOG_FILE" dentro de funciones que se ejecutan con sudo (el script completo debe ejecutarse con sudo).

Recomendación:
Siempre ejecutar el script como:

bash
sudo ./backup_enterprise.sh
4️⃣ Error de snapshot no encontrado en primer backup incremental
Descripción:
Al ejecutar el backup incremental por primera vez, el archivo snapshot.ref no existía y tar daba un warning, pero creaba el backup completo igualmente.

Causa:
tar con -g crea el snapshot si no existe, pero el warning puede confundir.

Solución:
No es un error crítico. Se puede crear el snapshot manualmente antes o simplemente ignorar el warning. En el script se redirigió stderr al log para no interrumpir la ejecución.

✅ Lecciones aprendidas
Verificar siempre la sintaxis de los comandos antes de ejecutarlos.

Usar ls para confirmar nombres de archivos.

Gestionar permisos adecuadamente (especialmente al escribir logs).

Documentar los errores para futuras referencias.

text

### 7. `logs/backup_enterprise.log` (ejemplo)

```log
2026-03-11 10:15:01 - === INICIO DEL BACKUP ===
2026-03-11 10:15:01 - Iniciando backup completo de /etc/ssh y /etc/apache2
2026-03-11 10:15:05 - ✅ Backup completo realizado: completo_20260311.tar.gz
2026-03-11 10:15:05 - Iniciando backup incremental de /home/kali/Documents
2026-03-11 10:15:08 - ✅ Backup incremental realizado: incremental_20260311_1015.tar.gz
2026-03-11 10:15:08 - Verificando integridad de /home/kali/backups/diarios/incremental_20260311_1015.tar.gz
2026-03-11 10:15:09 - ✅ Integridad verificada: /home/kali/backups/diarios/incremental_20260311_1015.tar.gz
2026-03-11 10:15:09 - === FIN DEL BACKUP ===