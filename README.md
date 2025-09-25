import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, Button, FlatList, TouchableOpacity, StyleSheet, CheckBox } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

useEffect(() => {
  const cargarTareas = async () => {
    const tareasGuardadas = await AsyncStorage.getItem('tareas');
    if (tareasGuardadas) {
      setTareas(JSON.parse(tareasGuardadas));
    }
  };
  cargarTareas();
}, []);

useEffect(() => {
  AsyncStorage.setItem('tareas', JSON.stringify(tareas));
}, [tareas]);

const agregarTarea = () => {
  if (tarea.trim() === '') return;
  const nuevaTarea = { id: Date.now().toString(), texto: tarea, completada: false };
  setTareas([...tareas, nuevaTarea]);
  setTarea('');
};

const toggleCompletada = (id) => {
  const nuevasTareas = tareas.map(t =>
    t.id === id ? { ...t, completada: !t.completada } : t
  );
  setTareas(nuevasTareas);
};

const eliminarTodas = () => {
  setTareas([]);
};

return (
  <View style={styles.container}>
    <Text style={styles.titulo}>Lista de Tareas</Text>

    <TextInput
      style={styles.input}
      placeholder="Escribí una tarea"
      value={tarea}
      onChangeText={setTarea}
    />

    <Button title="Guardar tarea" onPress={agregarTarea} />

    <FlatList
      data={tareas}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={styles.tareaItem}>
          <CheckBox
            value={item.completada}
            onValueChange={() => toggleCompletada(item.id)}
          />
          <Text style={item.completada ? styles.tareaCompletada : styles.tareaTexto}>
            {item.texto}
          </Text>
        </View>
      )}
    />

    <TouchableOpacity style={styles.botonEliminar} onPress={eliminarTodas}>
      <Text style={styles.botonTexto}>Eliminar todas las tareas</Text>
    </TouchableOpacity>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: '#fff',
  },
  titulo: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  input: {
    borderWidth: 1,
    padding: 10,
    marginBottom: 10,
  },
  tareaItem: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 5,
  },
  tareaTexto: {
    marginLeft: 10,
  },
  tareaCompletada: {
    marginLeft: 10,
    textDecorationLine: 'line-through',
    color: 'gray',
  },
  botonEliminar: {
    marginTop: 20,
    backgroundColor: 'red',
    padding: 10,
    alignItems: 'center',
  },
  botonTexto: {
    color: 'white',
    fontWeight: 'bold',
  },
});
