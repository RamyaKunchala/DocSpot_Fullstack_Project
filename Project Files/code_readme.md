Project Title: DocSpot fullstack project

Submitted by:K.RAMYA  - 22BFA02113
             K.POOJITHA - 22BFA02108


//2. Backend Code – server/server.js//
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();
const app = express();
const PORT = process.env.PORT || 5000;
app.use(cors());
app.use(express.json());
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology:
true })
.then(() => console.log("MongoDB connected"))
.catch((err) => console.error(err));
const appointmentRoutes = require('./routes/appointments');
app.use('/api/appointments', appointmentRoutes);
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
//3. MongoDB Model – models/Appointment.js//
const mongoose = require('mongoose');
const appointmentSchema = new mongoose.Schema({
 patientName: String,
 email: String,
 date: String,
 time: String,
 doctorName: String,
 reason: String,
});
module.exports = mongoose.model('Appointment', appointmentSchema);
//4. API Routes – routes/appointments.js//
const express = require('express');
const router = express.Router();
const Appointment = require('../models/Appointment');
router.post('/', async (req, res) => {
 try {
 const newAppointment = new Appointment(req.body);
 await newAppointment.save();
 res.status(201).json({ message: 'Appointment booked!' });
 } catch (err) {
 res.status(500).json({ error: err.message });
 }
});
router.get('/', async (req, res) => {
 try {
 const appointments = await Appointment.find();
 res.json(appointments);
 } catch (err) {
 res.status(500).json({ error: err.message });
 }
});
module.exports = router;
//5. Frontend Code – client/src/App.js//
import React, { useState } from 'react';
import axios from 'axios';
function App() {
 const [formData, setFormData] = useState({
 patientName: '',
 email: '',
 date: '',
 time: '',
 doctorName: '',
 reason: ''
 });
 const handleChange = (e) => {
 setFormData({...formData, [e.target.name]: e.target.value});
 };
 const handleSubmit = async (e) => {
 e.preventDefault();
 await axios.post('http://localhost:5000/api/appointments', formData);
 alert('Appointment booked successfully!');
 };
 return (
 <div className="App">
 <h1>DocSpot - Book Appointment</h1>
 <form onSubmit={handleSubmit}>
 <input name="patientName" placeholder="Patient Name" onChange={handleChange}
required />
 <input name="email" type="email" placeholder="Email" onChange={handleChange}
required />
 <input name="date" type="date" onChange={handleChange} required />
 <input name="time" type="time" onChange={handleChange} required />
 <input name="doctorName" placeholder="Doctor Name" onChange={handleChange}
required />
 <input name="reason" placeholder="Reason for Visit" onChange={handleChange}
required />
 <button type="submit">Book Appointment</button>
 </form>
 </div>
 );
}
export default App;
