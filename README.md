# Backend-for-authentication
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');
const app = express();
const port = 3000;
const secretKey = 'mysecretkey';

// Connect to MongoDB
mongoose.connect('mongodb://127.0.0.1:27017/mydatabase', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// ... (User schema and model, product schema, and verifyToken function remain the same)

app.use(bodyParser.json());

// Route for user signup
app.post('/signup', async (req, res) => {
  // ... (Existing code for user signup)

  // Generate a JWT token for the newly registered user
  const token = jwt.sign({ id: newUser._id, username: newUser.username }, secretKey, { expiresIn: '1h' });

  return res.status(201).json({ message: 'User created successfully', token });
});

// Route for user login
app.post('/login', async (req, res) => {
  const { username, password } = req.body;

  try {
    const user = await User.findOne({ username });

    if (!user) {
      return res.status(404).json({ message: 'User not found' });
    }

    const isPasswordValid = await bcrypt.compare(password, user.password);

    if (!isPasswordValid) {
      return res.status(401).json({ message: 'Invalid password' });
    }

    // Generate a JWT token for the logged-in user
    const token = jwt.sign({ id: user._id, username: user.username }, secretKey, { expiresIn: '1h' });

    return res.status(200).json({ message: 'Login successful', token });
  } catch (error) {
    console.error(error);
    return res.status(500).json({ message: 'An error occurred' });
  }
});

// Route for user logout (optional)

// Route for password reset request (forgot password)
app.post('/forgot-password', async (req, res) => {
  // Implement logic for sending a reset password email here
  // This route could send an email to the user's registered email with a reset password link
});

// Route for resetting the password with a token (reset password)
app.post('/reset-password/:token', async (req, res) => {
  const resetToken = req.params.token;
  // Implement logic to validate the reset token and update the user's password
});

// Route for refreshing an access token (refresh token)
app.post('/refresh-token', (req, res) => {
  // Implement logic to refresh the access token using a refresh token
  // This route should return a new access token to the client
});

// Account-related routes
app.get('/account', verifyToken, async (req, res) => {
  try {
    // Return account information for the authenticated user
    const user = await User.findById(req.user.id);
    return res.status(200).json({ message: 'Account info retrieved successfully', user });
  } catch (error) {
    console.error(error);
    return res.status(500).json({ message: 'An error occurred' });
  }
});

app.put('/account/update-password', verifyToken, async (req, res) => {
  try {
    const { newPassword } = req.body;
    const hashedPassword = await bcrypt.hash(newPassword, 10);
    await User.findByIdAndUpdate(req.user.id, { password: hashedPassword });
    return res.status(200).json({ message: 'Password updated successfully' });
  } catch (error) {
    console.error(error);
    return res.status(500).json({ message: 'An error occurred' });
  }
});

app.put('/account/update-details', verifyToken, async (req, res) => {
  try {
    const { full_name, age, gender, email } = req.body;
    await User.findByIdAndUpdate(req.user.id, { full_name, age, gender, email });
    return res.status(200).json({ message: 'Account details updated successfully' });
  } catch (error) {
    console.error(error);
    return res.status(500).json({ message: 'An error occurred' });
  }
});

// ... (Routes for other CRUD operations on products remain the same)

// Start the server
app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
