```markdown
# 🔁 Plan de Recuperación Documentado

Este plan describe paso a paso cómo actuar ante una pérdida de datos simulada, utilizando los backups realizados.

---

## 📌 Escenario

Se ha producido una pérdida de archivos en directorios críticos (`/etc/ssh` y `/etc/apache2`). Se procede a restaurar desde el backup más reciente.

---

## 🧾 Pasos a seguir

### 1. Identificar el backup más reciente
```bash
ls -lt ~/backups/diarios/completo_*.tar.gz | head -1
El más reciente aparecerá primero.

2. Verificar la integridad del backup
bash
tar -tzf ~/backups/diarios/completo_20260311.tar.gz > /dev/null && echo "✅ Backup íntegro" || echo "❌ Backup corrupto"
Si hay error, probar con otros backups.

3. Restaurar los archivos
bash
sudo tar -xzf ~/backups/diarios/completo_20260311.tar.gz -C /
Esto sobrescribe los archivos existentes con los del backup.

4. Verificar la restauración
bash
ls -la /etc/ssh
ls -la /etc/apache2
Comprobar que los archivos están presentes y con permisos adecuados.

5. Reiniciar servicios (si es necesario)
bash
sudo systemctl restart ssh
sudo systemctl restart apache2
6. Monitorear logs para confirmar que no hay errores
bash
journalctl -xe --since "5 minutes ago" | grep -i "ssh\|apache2"
📋 Ejemplo práctico 
bash
# 1. Listar backups disponibles
ls -lh ~/backups/diarios/

# 2. Ver contenido del backup sin extraer
tar -tzf ~/backups/diarios/completo_20260311.tar.gz | head -10

# 3. Extraer un archivo específico (prueba)
tar -xzf ~/backups/diarios/completo_20260311.tar.gz -C /tmp/restauracion ./etc/ssh/sshd_config

# 4. Verificar el archivo extraído
ls -la /tmp/restauracion/etc/ssh/
🧯 Contención del incidente (si el problema está activo)
Si el fallo proviene de un servicio web o BD, detenerlo inmediatamente:

bash
sudo systemctl stop apache2
sudo systemctl stop mysql
Luego preservar evidencias:

bash
mkdir ~/evidencias
sudo cp /var/log/apache2/error.log ~/evidencias/
sudo ps aux | sudo tee ~/evidencias/procesos.txt
Solo después proceder con la restauración.