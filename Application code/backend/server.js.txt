const express = require('express');
const mongoose = require('mongoose');
const app = express();
const port = 8080;

app.use(express.json());

// Connect to MongoDB
mongoose.connect('mongodb://mongo-service:27017/mydb', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => {
    console.log("Connected to MongoDB");
  })
  .catch((err) => {
    console.log("MongoDB connection error: ", err);
  });

// Define Item Schema and Model
const itemSchema = new mongoose.Schema({
  name: { type: String, required: true }
});
const Item = mongoose.model('Item', itemSchema);

// Endpoint to get all items
app.get('/api/items', async (req, res) => {
  try {
    const items = await Item.find();
    res.json(items);
  } catch (err) {
    res.status(500).send('Error fetching items');
  }
});

// Endpoint to add an item
app.post('/api/items', async (req, res) => {
  const { name } = req.body;
  const newItem = new Item({ name });

  try {
    await newItem.save();
    res.status(201).send('Item added');
  } catch (err) {
    res.status(500).send('Error adding item');
  }
});

app.listen(port, () => {
  console.log(`Backend API running on port ${port}`);
});
