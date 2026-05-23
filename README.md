import React, { useState } from 'react';
import { View, TextInput, TouchableOpacity, Text, StyleSheet, Alert, SafeAreaView } from 'react-native';
import * as FileSystem from 'expo-file-system';

export default function JuanchoStrap() {
  const [jsonInput, setJsonInput] = useState('{\n  "FFlagDebugDisableGraphicsSettings": false,\n  "FFlagEnableNewTechStack": true\n}');
  
  // Ruta crítica donde Roblox guarda su configuración en Android
  const ROBLOX_PATH = '/storage/emulated/0/Android/data/com.roblox.client/files/ClientSettings/ClientAppSettings.json';

  const injectFlags = async () => {
    try {
      // Parseo y filtro de seguridad (Flags parcheadas)
      const parsed = JSON.parse(jsonInput);
      const blacklisted = ["FFlagDebugDisableGraphicsSettings", "FFlagEnableNewTechStack"];
      
      blacklisted.forEach(f => {
        if (parsed.hasOwnProperty(f)) delete parsed[f];
      });

      const content = JSON.stringify(parsed, null, 2);
      
      // Escritura directa en el sistema de archivos
      await FileSystem.writeAsStringAsync(ROBLOX_PATH, content);
      
      Alert.alert('JuanchoStrap', 'Inyección exitosa. Flags limpias aplicadas.');
    } catch (e) {
      Alert.alert('Error', 'Error de acceso. Asegúrate de otorgar permisos de archivos: ' + e.message);
    }
  };

  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.title}>JUANCHOSTRAP</Text>
      <TextInput 
        style={styles.editor} 
        multiline 
        value={jsonInput} 
        onChangeText={setJsonInput}
        placeholderTextColor="#333"
      />
      <TouchableOpacity style={styles.button} onPress={injectFlags}>
        <Text style={styles.buttonText}>INJECT FLAGS</Text>
      </TouchableOpacity>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#000', padding: 20 },
  title: { color: '#00d4ff', fontSize: 18, marginBottom: 20, textAlign: 'center', fontFamily: 'monospace' },
  editor: { 
    flex: 1, 
    backgroundColor: '#0d0d0d', 
    color: '#00d4ff', 
    padding: 15, 
    borderWidth: 1, 
    borderColor: '#1a1a1a',
    borderRadius: 0,
    fontFamily: 'monospace'
  },
  button: { 
    marginTop: 20, 
    backgroundColor: '#0d0d0d', 
    padding: 15, 
    borderWidth: 1, 
    borderColor: '#00d4ff',
    borderRadius: 0
  },
  buttonText: { 
    color: '#00d4ff', 
    textAlign: 'center', 
    fontWeight: 'bold',
    fontFamily: 'monospace' 
  }
});
