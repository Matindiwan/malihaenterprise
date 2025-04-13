# malihaenterprise
import { useState } from 'react';
import Head from 'next/head';
import firebase from 'firebase/app';
import 'firebase/storage';

// Firebase Configuration (replace these with your actual Firebase project details)
const firebaseConfig = {
  apiKey: 'your-api-key',
  authDomain: 'your-auth-domain',
  projectId: 'your-project-id',
  storageBucket: 'your-storage-bucket',
  messagingSenderId: 'your-sender-id',
  appId: 'your-app-id',
};

// Initialize Firebase if not already initialized
if (!firebase.apps.length) {
  firebase.initializeApp(firebaseConfig);
} else {
  firebase.app();
}

const storage = firebase.storage();

// The homepage content
const services = [
  'Solar Solutions',
  'Electrical Projects',
  'Civil Projects',
  'Fire & Safety Projects',
  'Home Security',
  'Home Automation',
];

const Home = () => {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [message, setMessage] = useState('');
  const [status, setStatus] = useState('');
  const [file, setFile] = useState(null);

  // Form submission handler for contact form
  const handleContactSubmit = async (e) => {
    e.preventDefault();
    const res = await fetch('/api/contact', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ name, email, message }),
    });
    const data = await res.json();
    setStatus(data.status === 'success' ? 'Message sent successfully!' : 'Oops! Something went wrong.');
  };

  // File upload handler
  const handleFileChange = (e) => {
    if (e.target.files[0]) {
      setFile(e.target.files[0]);
    }
  };

  const handleFileUpload = () => {
    if (file) {
      const uploadTask = storage.ref(`files/${file.name}`).put(file);
      uploadTask.on(
        'state_changed',
        (snapshot) => {},
        (error) => console.log(error),
        () => {
          storage
            .ref('files')
            .child(file.name)
            .getDownloadURL()
            .then((url) => {
              console.log('File available at', url);
            });
        }
      );
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 text-gray-800">
      <Head>
        <title>Maliha Enterprise</title>
        <meta name="description" content="Maliha Enterprise: Your trusted partner in infrastructure and technology solutions." />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <header className="bg-white shadow-md p-4 flex justify-between items-center">
        <h1 className="text-2xl font-bold">Maliha Enterprise</h1>
      </header>

      <main className="p-6 space-y-6">
        <div className="flex justify-center">
          <img src="/logo-maliha-enterprise.png" alt="Maliha Enterprise Logo" width={200} height={200} />
        </div>
        <h1 className="text-4xl font-bold text-center">Maliha Enterprise</h1>
        <div className="text-center text-gray-600 text-lg">
          <p>Your trusted partner in infrastructure and technology solutions.</p>
        </div>

        <section className="grid md:grid-cols-2 gap-6">
          <div className="p-4 border border-gray-300">
            <h2 className="text-2xl font-semibold mb-2">About Us</h2>
            <p>
              Maliha Enterprise specializes in delivering comprehensive services in solar, electrical, civil, safety, and smart home automation projects. With a commitment to quality and innovation, we serve clients across residential, commercial, and industrial sectors.
            </p>
          </div>

          <div className="p-4 border border-gray-300">
            <h2 className="text-2xl font-semibold mb-2">Our Services</h2>
            <ul className="list-disc pl-5 space-y-1">
              {services.map((service) => (
                <li key={service}>{service}</li>
              ))}
            </ul>
          </div>

          <div className="p-4 border border-gray-300 md:col-span-2">
            <h2 className="text-2xl font-semibold mb-2">Contact Us</h2>
            <form onSubmit={handleContactSubmit} className="space-y-4">
              <input
                type="text"
                placeholder="Your Name"
                value={name}
                onChange={(e) => setName(e.target.value)}
                required
                className="w-full p-2 border border-gray-300"
              />
              <input
                type="email"
                placeholder="Your Email"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
                required
                className="w-full p-2 border border-gray-300"
              />
              <textarea
                placeholder="Your Message"
                value={message}
                onChange={(e) => setMessage(e.target.value)}
                required
                className="w-full p-2 border border-gray-300"
              />
              <button type="submit" className="py-2 px-4 bg-blue-600 text-white">Send Message</button>
              {status && <p className="text-sm">{status}</p>}
            </form>
          </div>
        </section>

        <div className="p-4 border border-gray-300">
          <h2 className="text-2xl font-semibold mb-2">File Upload</h2>
          <input type="file" onChange={handleFileChange} className="mb-4 p-2 border border-gray-300" />
          <button onClick={handleFileUpload} className="py-2 px-4 bg-green-600 text-white">Upload</button>
        </div>
      </main>

      <footer className="bg-white shadow-md p-4 text-center">
        <p>&copy; 2025 Maliha Enterprise. All rights reserved.</p>
      </footer>
    </div>
  );
};

// API handler for contact form submissions
export async function apiHandler(req, res) {
  if (req.method === 'POST') {
    const { name, email, message } = req.body;
    console.log('Received message:', { name, email, message });
    return res.status(200).json({ status: 'success' });
  } else {
    res.status(405).json({ status: 'error' });
  }
}

export default Home;
