# fitapp
// App.js
// Expo + React Native single-file starter fitness tracker
// Features:
// - Log workouts (type, duration, calories burned estimate)
// - Daily summary (total minutes, total calories)
// - Simple persistence with AsyncStorage
// - Add / delete entries
// - Basic UI (no extra UI libs)

import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, TextInput, TouchableOpacity, FlatList, Alert, KeyboardAvoidingView, Platform } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

const STORAGE_KEY = '@fitness_entries_v1';

export default function App() {
  const [type, setType] = useState('Running');
  const [duration, setDuration] = useState('30');
  const [entries, setEntries] = useState([]);

  useEffect(() => {
    loadEntries();
  }, []);

  useEffect(() => {
    saveEntries(entries);
  }, [entries]);

  const loadEntries = async () => {
    try {
      const raw = await AsyncStorage.getItem(STORAGE_KEY);
      if (raw) setEntries(JSON.parse(raw));
    } catch (e) {
      console.warn('Failed to load entries', e);
    }
  };

  const saveEntries = async (arr) => {
    try {
      await AsyncStorage.setItem(STORAGE_KEY, JSON.stringify(arr));
    } catch (e) {
      console.warn('Failed to save entries', e);
    }
  };

  const estimateCalories = (activityType, minutes) => {
    // crude MET-based estimator (adult average). You can customize values.
    const mets = {
      Running: 10,
      Walking: 3.5,
      Cycling: 8,
      Yoga: 3,
      Swimming: 7,
      Strength: 6,
      Other: 5,
    };
    const weightKg = 70; // default — you can add user profile to change this
    const met = mets[activityType] ?? mets['Other'];
    // calories = MET * weight(kg) * time(h)
    return Math.round(met * weightKg * (minutes / 60));
  };

  const addEntry = () => {
    const mins = parseInt(duration || '0', 10);
    if (!type || !mins || mins <= 0) {
      Alert.alert('Invalid', 'Select activity type and enter a positive duration');
      return;
    }
    const calories = estimateCalories(type, mins);
    const newEntry = {
      id: Date.now().toString(),
      type,
      duration: mins,
      calories,
      date: new Date().toISOString(),
    };
    setEntries(prev => [newEntry, ...prev]);
    setDuration('30');
  };

  const deleteEntry = (id) => {
    Alert.alert('Delete', 'Delete this entry?', [
      { text: 'Cancel', style: 'cancel' },
      { text: 'Delete', style: 'destructive', onPress: () => setEntries(prev => prev.filter(e => e.id !== id)) },
    ]);
  };

  const totalMinutes = entries.reduce((s, e) => s + e.duration, 0);
  const totalCalories = entries.reduce((s, e) => s + e.calories, 0);

  const renderItem = ({ item }) => (
    <View style={styles.item}>
      <View style={{ flex: 1 }}>
        <Text style={styles.itemTitle}>{item.type} • {item.duration} min</Text>
        <Text style={styles.itemSub}>{new Date(item.date).toLocaleString()}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={styles.cal}>{item.calories} kcal</Text>
        <TouchableOpacity onPress={() => deleteEntry(item.id)} style={styles.deleteBtn}>
          <Text style={styles.deleteTxt}>Delete</Text>
        </TouchableOpacity>
      </View>
    </View>
  );

  return (
    <KeyboardAvoidingView style={styles.container} behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
      <View style={styles.header}>
        <Text style={styles.title}>Fitness Tracker</Text>
        <Text style={styles.subtitle}>Quickly log workouts & calories</Text>
      </View>

      <View style={styles.summary}>
        <Text style={styles.summaryTxt}>Today: {totalMinutes} min • {totalCalories} kcal</Text>
      </View>

      <View style={styles.form}>
        <TextInput style={styles.input} value={type} onChangeText={setType} placeholder="Activity type (Running, Walking...)" />
        <TextInput style={styles.input} value={duration} onChangeText={setDuration} placeholder="Duration (minutes)" keyboardType="numeric" />
        <TouchableOpacity style={styles.addBtn} onPress={addEntry}>
          <Text style={styles.addTxt}>Add Workout</Text>
        </TouchableOpacity>
      </View>

      <FlatList
        data={entries}
        keyExtractor={(i) => i.id}
        renderItem={renderItem}
        contentContainerStyle={{ padding: 16, paddingBottom: 120 }}
        ListEmptyComponent={<Text style={{ textAlign: 'center', marginTop: 30 }}>No entries yet — add your first workout!</Text>}
      />

      <View style={styles.footer}>
        <Text style={{ fontSize: 12, color: '#666' }}>Tip: Edit the `weightKg` in estimateCalories or add a profile screen to personalize calories.</Text>
      </View>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#fff' },
  header: { padding: 20, paddingTop: 48, backgroundColor: '#f7f7f7', borderBottomWidth: 1, borderColor: '#eee' },
  title: { fontSize: 24, fontWeight: '700' },
  subtitle: { color: '#666', marginTop: 6 },
  summary: { padding: 12, alignItems: 'center' },
  summaryTxt: { fontSize: 16, fontWeight: '600' },
  form: { paddingHorizontal: 16, gap: 8 },
  input: { borderWidth: 1, borderColor: '#ddd', borderRadius: 8, padding: 10, marginBottom: 8 },
  addBtn: { backgroundColor: '#222', padding: 12, borderRadius: 8, alignItems: 'center' },
  addTxt: { color: '#fff', fontWeight: '700' },
  item: { flexDirection: 'row', alignItems: 'center', padding: 12, borderRadius: 10, borderWidth: 1, borderColor: '#eee', marginBottom: 10, backgroundColor: '#fafafa' },
  itemTitle: { fontWeight: '700' },
  itemSub: { color: '#666', fontSize: 12 },
  cal: { fontWeight: '700' },
  deleteBtn: { marginTop: 8 },
  deleteTxt: { color: '#b00' },
  footer: { padding: 12, borderTopWidth: 1, borderColor: '#eee' },
});

/*
README (usage)

1) Prerequisites:
   - Install Node.js and npm
   - Install Expo CLI: npm install -g expo-cli
   - Create a new Expo project or copy this App.js into an existing Expo project.
   - Install AsyncStorage: expo install @react-native-async-storage/async-storage

2) Run locally:
   - expo start
   - Use the Expo Go app (Android/iOS) or run on simulators.

3) Next improvements you can ask me for:
   - Add user profile (weight, age) to customize calories
   - Add charts (weekly/monthly) using a chart library
   - Add authentication + cloud sync (Firebase)
   - Add step counter (native permissions) and background activity
   - Add reminders/notifications

If you want, I can:
 - Convert this to a full multi-screen app with React Navigation
 - Build the same app in Flutter (Dart)
 - Add a backend and cloud sync (Firebase/Firestore)
*/
