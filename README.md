# schema

const mongoose = require('mongoose');

const authSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    unique: true,
    match: [/^\S+@\S+\.\S+$/, 'Invalid email address']
  },
  password: {
    type: String,
    required: true,
    minlength: 6
  },
  role: {
    type: String,
    enum: ['user', 'club-admin', 'admin'],
    default: 'user'
  }
}, { timestamps: true });

module.exports = mongoose.model('Auth', authSchema);
-------------------------------------------------------------------------------------------------------------------
 const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  auth: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Auth',
    required: true
  },
  name: String,
  age: Number,
  yearOfStudy: String,
  department: String,
  isClubMember: { type: Boolean, default: false },
  clubName: { type: String, default: null }  // optional
}, { timestamps: true });

module.exports = mongoose.model('User', userSchema;

------------------------------------------------------------------------------------------------------------------
const mongoose = require('mongoose');

const clubAdminSchema = new mongoose.Schema({
  auth: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Auth',
    required: true
  },
  name: String,
  club: String,
  position: String,
  year: String,
  department: String,
  universityRollNo: String
}, { timestamps: true });

module.exports = mongoose.model('ClubAdmin', clubAdminSchema);
---------------------------------------------------------------------------------------------------------------
const mongoose = require('mongoose');

const eventSchema = new mongoose.Schema({
  title: { type: String, required: true },
  description: String,
  eventType: { type: String, enum: ['Hackathon', 'Workshop', 'Seminar', 'Other'] },
  date: { type: Date, required: true },
  venue: String,
  createdBy: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'ClubAdmin',
    required: true
  },
  media: [
    {
      type: { type: String, enum: ['image', 'video'] },
      url: String
    }
  ],
  registeredUsers: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User'
    }
  ],
  comments: [
    {
      user: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
      text: String,
      timestamp: { type: Date, default: Date.now }
    }
  ],
  feed…

---------------------------------------------------------------------------------------------------------  
const mongoose = require('mongoose');

const eventStatsSchema = new mongoose.Schema({
  event: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Event',
    required: true
  },
  views: { type: Number, default: 0 },
  registrations: { type: Number, default: 0 },
  commentsCount: { type: Number, default: 0 }
}, { timestamps: true });

module.exports = mongoose.model('EventStats', eventStatsSchema);

---------------------------------------------------------------------------------------------------------------
 const mongoose = require('mongoose');

const eventRegistrationSchema = new mongoose.Schema({
  eventId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Event',
    required: true
  },

  userId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },

  qrCode: {
    type: String,
    required: true,
    unique: true // ensures one QR per registration
  },

  isScanned: {
    type: Boolean,
    default: false
  },

  scannedAt: {
    type: Date,
    default: null
  }

}, { timestamps: true });

eventRegistrationSchema.index({ eventId: 1, userId: 1 }, { unique: true });

module.exports = mongoose.model('EventRegistration', eventRegistrationSchema);
