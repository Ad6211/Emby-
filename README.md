# Emby-
# 1. Installer patch si nécessaire
sudo apt update && sudo apt install patch git -y

# 2. Télécharger emby-unlocked
cd ~
git clone https://gitcode.com/gh_mirrors/em/emby-unlocked
cd emby-unlocked

# 3. Arrêter Emby
sudo systemctl stop emby-server

# 4. Sauvegarder les fichiers originaux (au cas où)
sudo cp /opt/emby-server/system/EmbyWeb/connectionmanager.js /opt/emby-server/system/EmbyWeb/connectionmanager.js.backup 2>/dev/null
sudo cp /opt/emby-server/Emby.Server.Implementations.dll /opt/emby-server/Emby.Server.Implementations.dll.backup 2>/dev/null

# 5. Appliquer le patch (trouver automatiquement le chemin)
EMBY_PATH="/opt/emby-server"
CONN_FILE=$(find $EMBY_PATH -name "connectionmanager.js" 2>/dev/null | head -1)

if [ -n "$CONN_FILE" ]; then
    sudo cp replacements/connectionmanager.js "$CONN_FILE"
    echo "✅ connectionmanager.js remplacé"
else
    echo "❌ connectionmanager.js non trouvé"
fi

# 6. Appliquer le patch DLL si possible
sudo patch /opt/emby-server/Emby.Server.Implementations.dll < patches/PluginSecurityManager.cs.patch 2>/dev/null && echo "✅ Patch appliqué" || echo "⚠️ Patch ignoré (peut-être déjà fait)"

# 7. Redémarrer
sudo systemctl restart emby-server
echo "✅ Fait ! Allez sur http://VOTRE_RASPBERRY:8096 et entrez n'importe quelle clé"
