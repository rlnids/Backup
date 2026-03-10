markdown
# 🧠 Explicación detallada de comandos utilizados

Este archivo documenta cada comando empleado durante la práctica, con ejemplos y emojis para facilitar la comprensión.

---

## 📦 Comandos de `tar` (backup)

### Backup completo
```bash
sudo tar -czf ~/backups/diarios/completo_$(date +%Y%m%d).tar.gz /etc/ssh /etc/apache2
sudo → Ejecuta como superusuario (necesario para leer /etc/ssh y /etc/apache2).

tar → Herramienta de archivado.

-c → Crea un nuevo archivo.

-z → Comprime con gzip.

-f → Especifica el nombre del archivo de salida.

$(date +%Y%m%d) → Fecha actual en formato YYYYMMDD (ej. 20260311).

/etc/ssh /etc/apache2 → Directorios a respaldar.

Backup incremental (simulado con snapshot)
bash
sudo tar -czf ~/backups/diarios/incremental_$(date +%Y%m%d_%H%M).tar.gz -g ~/backups/snapshot.ref /home/kali/Documents
-g → Usa un archivo de metadatos (snapshot) para controlar cambios incrementales.

La primera vez crea el snapshot y un backup completo; las siguientes veces solo incluye cambios.

Verificar contenido sin extraer
bash
tar -tzf ~/backups/diarios/completo_20260311.tar.gz | head -10
-t → Lista el contenido.

-z → Indica compresión gzip.

-f → Archivo a inspeccionar.

| head -10 → Muestra solo las primeras 10 líneas.

Extraer un archivo específico
bash
tar -xzf ~/backups/diarios/completo_20260311.tar.gz -C /tmp/restauracion ./etc/ssh/sshd_config
-x → Extrae.

-C → Directorio destino.

./etc/ssh/sshd_config → Ruta relativa dentro del backup del archivo a extraer.

🖥️ Comandos de monitoreo
journalctl (logs del sistema)
bash
journalctl -xe --since "10 minutes ago" | grep -i "backup\|error"
-x → Añade explicaciones.

-e → Va al final del log.

--since → Filtra por tiempo.

Útil para detectar errores recientes.

top (procesos en tiempo real)
bash
top -b -n 1 | head -20
-b → Modo batch (no interactivo).

-n 1 → Una sola iteración.

head -20 → Muestra las primeras 20 líneas (los procesos más pesados).

df -h (espacio en disco)
bash
df -h
Muestra el espacio usado/disponible en todas las particiones en formato legible.

ss -tulpn (conexiones de red)
bash
sudo ss -tulpn
-t → TCP.

-u → UDP.

-l → Solo sockets en escucha.

-p → Muestra el proceso asociado.

-n → No resuelve nombres (más rápido).

🛑 Comandos de contención
Detener servicios
bash
sudo systemctl stop apache2   # Para el servidor web
sudo systemctl stop mysql     # Para la base de datos
Preservar evidencia (logs y procesos)
bash
sudo cp /var/log/apache2/error.log ~/evidencias/
sudo ps aux | sudo tee ~/evidencias/lista_procesos.txt
🔁 Comandos de recuperación
Restaurar backup completo
bash
sudo tar -xzf ~/backups/diarios/completo_20260311.tar.gz -C /
Restaura todo el contenido en la raíz (sobrescribe archivos existentes).

Verificar restauración
bash
ls -la /etc/ssh /etc/apache2
📝 Notas importantes
Siempre verifica la integridad del backup antes de restaurar.

Usa sudo cuando sea necesario para permisos de lectura/escritura.

Documenta cualquier error en FALLOS_Y_SOLUCIONES.md.