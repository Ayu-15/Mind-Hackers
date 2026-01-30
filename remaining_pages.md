# Remaining Frontend Pages

## 📁 frontend/src/pages/Offers.jsx

```jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import Navbar from '../components/Navbar';
import { useCart } from '../context/CartContext';
import './Offers.css';

const Offers = () => {
  const [code, setCode] = useState('');
  const [message, setMessage] = useState('');
  const [loading, setLoading] = useState(false);
  const { applyDiscount, cartDiscount } = useCart();
  const navigate = useNavigate();

  const availableOffers = [
    { code: 'SAVE10', description: 'Get 10% off on your order', type: '10% OFF' },
    { code: 'SAVE20', description: 'Get 20% off on your order', type: '20% OFF' },
    { code: 'FLAT100', description: 'Get ₹100 off on your order', type: '₹100 OFF' },
    { code: 'FLAT200', description: 'Get ₹200 off on your order', type: '₹200 OFF' },
  ];

  const handleApply = async (offerCode) => {
    setLoading(true);
    setMessage('');
    try {
      const result = await applyDiscount(offerCode || code);
      setMessage(result.message);
      setTimeout(() => {
        navigate('/cart');
      }, 2000);
    } catch (error) {
      setMessage(error.response?.data?.message || 'Invalid discount code');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <h1 className="page-title">Offers & Discounts</h1>

        <div className="offers-content">
          <div className="card">
            <h2>Apply Discount Code</h2>
            <div className="code-input-group">
              <input
                type="text"
                className="input"
                placeholder="Enter discount code"
                value={code}
                onChange={(e) => setCode(e.target.value.toUpperCase())}
              />
              <button
                className="btn btn-primary"
                onClick={() => handleApply()}
                disabled={loading || !code}
              >
                Apply
              </button>
            </div>
            {message && (
              <div className={`alert mt-3 ${message.includes('Invalid') ? 'alert-error' : 'alert-success'}`}>
                {message}
              </div>
            )}
            {cartDiscount > 0 && (
              <div className="current-discount">
                🎉 Current Discount: ₹{cartDiscount.toLocaleString()}
              </div>
            )}
          </div>

          <div className="offers-grid">
            {availableOffers.map((offer, index) => (
              <div key={index} className="offer-card card">
                <div className="offer-badge">{offer.type}</div>
                <h3>{offer.code}</h3>
                <p>{offer.description}</p>
                <button
                  className="btn btn-secondary mt-2"
                  onClick={() => handleApply(offer.code)}
                  disabled={loading}
                >
                  Apply Offer
                </button>
              </div>
            ))}
          </div>
        </div>
      </div>
    </div>
  );
};

export default Offers;
```

## 📁 frontend/src/pages/Offers.css

```css
.offers-content {
  max-width: 900px;
  margin: 0 auto;
}

.code-input-group {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
}

.current-discount {
  background: rgba(39, 174, 96, 0.2);
  border: 1px solid #27ae60;
  color: #2ecc71;
  padding: 1rem;
  border-radius: 8px;
  margin-top: 1rem;
  text-align: center;
  font-weight: 600;
}

.offers-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-top: 2rem;
}

.offer-card {
  position: relative;
  text-align: center;
  padding: 2rem;
}

.offer-badge {
  position: absolute;
  top: -10px;
  right: -10px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-weight: bold;
  font-size: 0.9rem;
}

.offer-card h3 {
  font-size: 1.5rem;
  margin: 1rem 0;
  color: #6c63ff;
}

.offer-card p {
  color: rgba(255, 255, 255, 0.7);
  margin-bottom: 1rem;
}
```

## 📁 frontend/src/pages/Checkout.jsx

```jsx
import { useNavigate } from 'react-router-dom';
import { useState } from 'react';
import axios from 'axios';
import Navbar from '../components/Navbar';
import { useCart } from '../context/CartContext';
import './Checkout.css';

const Checkout = () => {
  const { cart, cartTotal, cartDiscount, finalTotal } = useCart();
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  const handlePlaceOrder = async () => {
    setLoading(true);
    try {
      const transactionId = `TXN-${Date.now()}`;
      const { data } = await axios.post('http://localhost:5000/api/orders/create', {
        paymentMethod: 'Pending',
        transactionId,
      });
      navigate(`/payment/${data._id}`);
    } catch (error) {
      alert('Error creating order');
    } finally {
      setLoading(false);
    }
  };

  if (!cart || cart.items.length === 0) {
    return (
      <div className="page">
        <Navbar />
        <div className="container">
          <div className="empty-cart">
            <h2>No items in cart</h2>
            <button className="btn btn-primary" onClick={() => navigate('/scan')}>
              Start Shopping
            </button>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <h1 className="page-title">Checkout</h1>

        <div className="checkout-content">
          <div className="checkout-items">
            <div className="card">
              <h2>Order Summary</h2>
              {cart.items.map((item) => (
                <div key={item._id} className="checkout-item">
                  <div className="item-info">
                    <span>{item.product.name}</span>
                    <span className="item-qty">x{item.quantity}</span>
                  </div>
                  <span className="item-price">
                    ₹{(item.price * item.quantity).toLocaleString()}
                  </span>
                </div>
              ))}
            </div>
          </div>

          <div className="checkout-summary card">
            <h2>Payment Summary</h2>
            <div className="summary-row">
              <span>Subtotal:</span>
              <span>₹{cartTotal.toLocaleString()}</span>
            </div>
            {cartDiscount > 0 && (
              <div className="summary-row discount">
                <span>Discount:</span>
                <span>-₹{cartDiscount.toLocaleString()}</span>
              </div>
            )}
            <div className="summary-row total">
              <span>Total Amount:</span>
              <span>₹{finalTotal.toLocaleString()}</span>
            </div>
            <button
              className="btn btn-primary btn-lg mt-3"
              onClick={handlePlaceOrder}
              disabled={loading}
            >
              {loading ? 'Processing...' : 'Proceed to Payment'}
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default Checkout;
```

## 📁 frontend/src/pages/Checkout.css

```css
.checkout-content {
  display: grid;
  grid-template-columns: 1fr 400px;
  gap: 2rem;
  margin-top: 2rem;
}

.checkout-items {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.checkout-item {
  display: flex;
  justify-content: space-between;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.checkout-item:last-child {
  border-bottom: none;
}

.item-info {
  display: flex;
  gap: 1rem;
  align-items: center;
}

.item-qty {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.9rem;
}

.item-price {
  font-weight: 600;
  color: #27ae60;
}

.checkout-summary {
  position: sticky;
  top: 100px;
  height: fit-content;
}

@media (max-width: 968px) {
  .checkout-content {
    grid-template-columns: 1fr;
  }
  
  .checkout-summary {
    position: static;
  }
}
```

## 📁 frontend/src/pages/Payment.jsx

```jsx
import { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import axios from 'axios';
import Navbar from '../components/Navbar';
import './Payment.css';

const Payment = () => {
  const { orderId } = useParams();
  const [order, setOrder] = useState(null);
  const [paymentMethod, setPaymentMethod] = useState('UPI');
  const [loading, setLoading] = useState(false);
  const [upiId, setUpiId] = useState('');
  const [cardNumber, setCardNumber] = useState('');
  const [walletType, setWalletType] = useState('Paytm');
  const navigate = useNavigate();

  useEffect(() => {
    const fetchOrder = async () => {
      try {
        const { data } = await axios.get(`http://localhost:5000/api/orders/${orderId}`);
        setOrder(data);
      } catch (error) {
        alert('Order not found');
        navigate('/cart');
      }
    };
    fetchOrder();
  }, [orderId, navigate]);

  const handlePayment = async () => {
    setLoading(true);
    try {
      const { data } = await axios.post('http://localhost:5000/api/payment/process', {
        orderId,
        paymentMethod,
        upiId,
        cardNumber,
        walletType,
      });

      if (data.success) {
        navigate(`/payment-success/${orderId}`);
      } else {
        alert('Payment failed. Please try again.');
      }
    } catch (error) {
      alert('Payment processing error');
    } finally {
      setLoading(false);
    }
  };

  if (!order) {
    return (
      <div className="page">
        <Navbar />
        <div className="flex-center" style={{ height: '60vh' }}>
          <div className="spinner"></div>
        </div>
      </div>
    );
  }

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <h1 className="page-title">Payment</h1>

        <div className="payment-container">
          <div className="card">
            <h2>Select Payment Method</h2>
            
            <div className="payment-methods">
              <div
                className={`payment-option ${paymentMethod === 'UPI' ? 'active' : ''}`}
                onClick={() => setPaymentMethod('UPI')}
              >
                <span className="payment-icon">📱</span>
                <span>UPI</span>
              </div>
              <div
                className={`payment-option ${paymentMethod === 'Card' ? 'active' : ''}`}
                onClick={() => setPaymentMethod('Card')}
              >
                <span className="payment-icon">💳</span>
                <span>Card</span>
              </div>
              <div
                className={`payment-option ${paymentMethod === 'Wallet' ? 'active' : ''}`}
                onClick={() => setPaymentMethod('Wallet')}
              >
                <span className="payment-icon">👛</span>
                <span>Wallet</span>
              </div>
            </div>

            {paymentMethod === 'UPI' && (
              <div className="payment-form">
                <label>UPI ID</label>
                <input
                  type="text"
                  className="input"
                  placeholder="yourname@upi"
                  value={upiId}
                  onChange={(e) => setUpiId(e.target.value)}
                />
              </div>
            )}

            {paymentMethod === 'Card' && (
              <div className="payment-form">
                <label>Card Number</label>
                <input
                  type="text"
                  className="input"
                  placeholder="1234 5678 9012 3456"
                  value={cardNumber}
                  onChange={(e) => setCardNumber(e.target.value)}
                  maxLength={19}
                />
              </div>
            )}

            {paymentMethod === 'Wallet' && (
              <div className="payment-form">
                <label>Select Wallet</label>
                <select
                  className="input"
                  value={walletType}
                  onChange={(e) => setWalletType(e.target.value)}
                >
                  <option>Paytm</option>
                  <option>PhonePe</option>
                  <option>Google Pay</option>
                  <option>Amazon Pay</option>
                </select>
              </div>
            )}

            <div className="payment-amount">
              <span>Amount to Pay:</span>
              <span className="amount">₹{order.finalPrice.toLocaleString()}</span>
            </div>

            <button
              className="btn btn-primary btn-lg mt-3"
              onClick={handlePayment}
              disabled={loading}
            >
              {loading ? 'Processing Payment...' : 'Pay Now'}
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default Payment;
```

## 📁 frontend/src/pages/Payment.css

```css
.payment-container {
  max-width: 600px;
  margin: 2rem auto;
}

.payment-methods {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  margin: 2rem 0;
}

.payment-option {
  padding: 2rem 1rem;
  border: 2px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.payment-option:hover {
  border-color: #6c63ff;
  transform: translateY(-5px);
}

.payment-option.active {
  border-color: #6c63ff;
  background: rgba(108, 99, 255, 0.1);
}

.payment-icon {
  font-size: 3rem;
}

.payment-form {
  margin: 2rem 0;
}

.payment-form label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 500;
}

.payment-amount {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  background: rgba(39, 174, 96, 0.1);
  border: 1px solid #27ae60;
  border-radius: 8px;
  margin-top: 2rem;
}

.payment-amount .amount {
  font-size: 2rem;
  font-weight: bold;
  color: #27ae60;
}
```

## 📁 frontend/src/pages/PaymentSuccess.jsx

```jsx
import { useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import Navbar from '../components/Navbar';
import './PaymentSuccess.css';

const PaymentSuccess = () => {
  const { orderId } = useParams();
  const navigate = useNavigate();

  useEffect(() => {
    const timer = setTimeout(() => {
      navigate(`/bill/${orderId}`);
    }, 3000);

    return () => clearTimeout(timer);
  }, [orderId, navigate]);

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <div className="success-container">
          <div className="success-animation">
            <div className="checkmark">✓</div>
          </div>
          <h1 className="success-title">Payment Successful!</h1>
          <p className="success-message">
            Your payment has been processed successfully
          </p>
          <div className="success-loader">
            <div className="loader-bar"></div>
          </div>
          <p className="redirect-text">Redirecting to your bill...</p>
        </div>
      </div>
    </div>
  );
};

export default PaymentSuccess;
```

## 📁 frontend/src/pages/PaymentSuccess.css

```css
.success-container {
  max-width: 500px;
  margin: 4rem auto;
  text-align: center;
}

.success-animation {
  margin-bottom: 2rem;
}

.checkmark {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background: linear-gradient(135deg, #27ae60, #2ecc71);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 4rem;
  color: white;
  margin: 0 auto;
  animation: checkmarkPop 0.5s ease-out;
}

@keyframes checkmarkPop {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.success-title {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  color: #27ae60;
}

.success-message {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1.2rem;
  margin-bottom: 2rem;
}

.success-loader {
  width: 100%;
  height: 4px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 2px;
  margin: 2rem 0;
  overflow: hidden;
}

.success-loader .loader-bar {
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, #27ae60, #2ecc71);
  animation: loading 3s ease-in-out;
}

.redirect-text {
  color: rgba(255, 255, 255, 0.5);
  font-size: 0.9rem;
}
```

## 📁 frontend/src/pages/Bill.jsx

```jsx
import { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import axios from 'axios';
import { QRCodeSVG } from 'qrcode.react';
import Navbar from '../components/Navbar';
import './Bill.css';

const Bill = () => {
  const { orderId } = useParams();
  const [order, setOrder] = useState(null);
  const [qrData, setQrData] = useState(null);
  const navigate = useNavigate();

  useEffect(() => {
    const fetchOrder = async () => {
      try {
        const { data } = await axios.get(`http://localhost:5000/api/orders/${orderId}`);
        setOrder(data);

        // Generate QR codes
        const qrResponse = await axios.post(
          `http://localhost:5000/api/orders/${orderId}/generate-qr`
        );
        setQrData(qrResponse.data);
      } catch (error) {
        alert('Error fetching bill');
        navigate('/home');
      }
    };
    fetchOrder();
  }, [orderId, navigate]);

  if (!order || !qrData) {
    return (
      <div className="page">
        <Navbar />
        <div className="flex-center" style={{ height: '60vh' }}>
          <div className="spinner"></div>
        </div>
      </div>
    );
  }

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <div className="bill-container">
          <div className="card">
            <div className="bill-header">
              <h1>🛒 ScanGo</h1>
              <p>Invoice</p>
            </div>

            <div className="bill-info">
              <div className="info-row">
                <span>Order ID:</span>
                <span>{order._id.slice(-8).toUpperCase()}</span>
              </div>
              <div className="info-row">
                <span>Transaction ID:</span>
                <span>{order.transactionId}</span>
              </div>
              <div className="info-row">
                <span>Date:</span>
                <span>{new Date(order.createdAt).toLocaleString()}</span>
              </div>
              <div className="info-row">
                <span>Payment Method:</span>
                <span>{order.paymentMethod}</span>
              </div>
            </div>

            <div className="bill-items">
              <h3>Items</h3>
              {order.items.map((item, index) => (
                <div key={index} className="bill-item">
                  <div>
                    <div className="item-name">{item.name}</div>
                    <div className="item-details">
                      Qty: {item.quantity} × ₹{item.price.toLocaleString()}
                    </div>
                  </div>
                  <div className="item-total">
                    ₹{(item.quantity * item.price).toLocaleString()}
                  </div>
                </div>
              ))}
            </div>

            <div className="bill-summary">
              <div className="summary-row">
                <span>Subtotal:</span>
                <span>₹{order.totalPrice.toLocaleString()}</span>
              </div>
              {order.discount > 0 && (
                <div className="summary-row discount">
                  <span>Discount:</span>
                  <span>-₹{order.discount.toLocaleString()}</span>
                </div>
              )}
              <div className="summary-row total">
                <span>Total Paid:</span>
                <span>₹{order.finalPrice.toLocaleString()}</span>
              </div>
            </div>

            <div className="bill-qr">
              <h3>Bill QR Code</h3>
              <div className="qr-code">
                <img src={qrData.billQR} alt="Bill QR" />
              </div>
            </div>

            <button
              className="btn btn-primary btn-lg mt-3"
              onClick={() => navigate(`/exit-qr/${orderId}`)}
            >
              Proceed to Exit
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default Bill;
```

## 📁 frontend/src/pages/Bill.css

```css
.bill-container {
  max-width: 700px;
  margin: 2rem auto;
}

.bill-header {
  text-align: center;
  padding-bottom: 2rem;
  border-bottom: 2px solid rgba(255, 255, 255, 0.1);
  margin-bottom: 2rem;
}

.bill-header h1 {
  font-size: 3rem;
  margin-bottom: 0.5rem;
}

.bill-info {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 2rem;
}

.info-row {
  display: flex;
  justify-content: space-between;
  padding: 0.75rem 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
}

.info-row span:first-child {
  color: rgba(255, 255, 255, 0.6);
}

.info-row span:last-child {
  font-weight: 600;
}

.bill-items {
  margin: 2rem 0;
}

.bill-items h3 {
  margin-bottom: 1.5rem;
}

.bill-item {
  display: flex;
  justify-content: space-between;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.item-name {
  font-weight: 600;
  margin-bottom: 0.25rem;
}

.item-details {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.9rem;
}

.item-total {
  font-weight: 600;
  color: #27ae60;
}

.bill-summary {
  margin: 2rem 0;
  padding: 1.5rem;
  background: rgba(255, 255, 255, 0.03);
  border-radius: 12px;
}

.bill-qr {
  text-align: center;
  margin: 2rem 0;
}

.bill-qr h3 {
  margin-bottom: 1.5rem;
}

.qr-code {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  display: inline-block;
}

.qr-code img {
  width: 250px;
  height: 250px;
}
```

## 📁 frontend/src/pages/ExitQR.jsx

```jsx
import { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import axios from 'axios';
import Navbar from '../components/Navbar';
import './ExitQR.css';

const ExitQR = () => {
  const { orderId } = useParams();
  const [qrData, setQrData] = useState(null);
  const [exited, setExited] = useState(false);
  const navigate = useNavigate();

  useEffect(() => {
    const fetchQR = async () => {
      try {
        const { data } = await axios.post(
          `http://localhost:5000/api/orders/${orderId}/generate-qr`
        );
        setQrData(data);
      } catch (error) {
        alert('Error loading exit QR');
        navigate('/home');
      }
    };
    fetchQR();
  }, [orderId, navigate]);

  const handleExit = async () => {
    try {
      await axios.put(`http://localhost:5000/api/orders/${orderId}/exit`);
      setExited(true);
      setTimeout(() => {
        navigate('/profile');
      }, 3000);
    } catch (error) {
      alert('Error marking exit');
    }
  };

  if (!qrData) {
    return (
      <div className="page">
        <Navbar />
        <div className="flex-center" style={{ height: '60vh' }}>
          <div className="spinner"></div>
        </div>
      </div>
    );
  }

  if (exited) {
    return (
      <div className="page">
        <Navbar />
        <div className="container">
          <div className="exit-success">
            <div className="success-icon">✓</div>
            <h1>Thank You for Shopping!</h1>
            <p>EAS tags deactivated. You may exit the store.</p>
            <p className="redirect-text">Redirecting to profile...</p>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <div className="exit-container">
          <div className="card">
            <h1 className="exit-title">Exit Gate QR</h1>
            <p className="exit-instruction">
              Show this QR code at the exit gate to deactivate EAS tags
            </p>

            <div className="exit-qr">
              <div className="qr-display">
                <img src={qrData.exitQR} alt="Exit QR" />
              </div>
            </div>

            <div className="alert alert-info">
              <strong>Instructions:</strong>
              <ul>
                <li>Present this QR code to security</li>
                <li>Wait for EAS tag deactivation</li>
                <li>Exit through the designated gate</li>
              </ul>
            </div>

            <button className="btn btn-primary btn-lg mt-3" onClick={handleExit}>
              I've Exited the Store
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default ExitQR;
```

## 📁 frontend/src/pages/ExitQR.css

```css
.exit-container {
  max-width: 600px;
  margin: 2rem auto;
  text-align: center;
}

.exit-title {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.exit-instruction {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1.1rem;
  margin-bottom: 2rem;
}

.exit-qr {
  margin: 2rem 0;
}

.qr-display {
  background: white;
  padding: 3rem;
  border-radius: 16px;
  display: inline-block;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.qr-display img {
  width: 300px;
  height: 300px;
}

.alert ul {
  text-align: left;
  margin-top: 1rem;
  padding-left: 1.5rem;
}

.alert li {
  margin: 0.5rem 0;
}

.exit-success {
  text-align: center;
  padding: 4rem;
}

.success-icon {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background: linear-gradient(135deg, #27ae60, #2ecc71);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 5rem;
  color: white;
  margin: 0 auto 2rem;
  animation: checkmarkPop 0.5s ease-out;
}

.exit-success h1 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  color: #27ae60;
}

.exit-success p {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1.2rem;
  margin-bottom: 1rem;
}

.redirect-text {
  color: rgba(255, 255, 255, 0.5);
  font-size: 0.9rem;
}
```

## 📁 frontend/src/pages/Profile.jsx

```jsx
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import axios from 'axios';
import Navbar from '../components/Navbar';
import { useAuth } from '../context/AuthContext';
import './Profile.css';

const Profile = () => {
  const { user, logout } = useAuth();
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(true);
  const navigate = useNavigate();

  useEffect(() => {
    const fetchOrders = async () => {
      try {
        const { data } = await axios.get('http://localhost:5000/api/orders');
        setOrders(data);
      } catch (error) {
        console.error('Error fetching orders:', error);
      } finally {
        setLoading(false);
      }
    };
    fetchOrders();
  }, []);

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <div className="page">
      <Navbar />
      <div className="container">
        <div className="profile-container">
          <div className="profile-header card">
            <div className="profile-avatar">👤</div>
            <div className="profile-info">
              <h1>{user?.name}</h1>
              <p>{user?.email}</p>
              <p>{user?.phone}</p>
            </div>
            <button className="btn btn-danger" onClick={handleLogout}>
              Logout
            </button>
          </div>

          <div className="transaction-history card">
            <h2>Transaction History</h2>
            {loading ? (
              <div className="flex-center" style={{ padding: '2rem' }}>
                <div className="spinner"></div>
              </div>
            ) : orders.length === 0 ? (
              <div className="no-orders">
                <p>No orders yet</p>
                <button className="btn btn-primary" onClick={() => navigate('/scan')}>
                  Start Shopping
                </button>
              </div>
            ) : (
              <div className="orders-list">
                {orders.map((order) => (
                  <div key={order._id} className="order-item">
                    <div className="order-header">
                      <div>
                        <div className="order-id">Order #{order._id.slice(-8).toUpperCase()}</div>
                        <div className="order-date">
                          {new Date(order.createdAt).toLocaleDateString()}
                        </div>
                      </div>
                      <div className="order-amount">₹{order.finalPrice.toLocaleString()}</div>
                    </div>
                    <div className="order-details">
                      <div className="order-status">
                        <span className={`status-badge ${order.status.toLowerCase()}`}>
                          {order.status}
                        </span>
                      </div>
                      <div className="order-items-count">
                        {order.items.length} item{order.items.length > 1 ? 's' : ''}
                      </div>
                      <button
                        className="btn btn-secondary btn-sm"
                        onClick={() => navigate(`/bill/${order._id}`)}
                      >
                        View Bill
                      </button>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        </div>
      </div>
    </div>
  );
};

export default Profile;
```

## 📁 frontend/src/pages/Profile.css

```css
.profile-container {
  max-width: 900px;
  margin: 2rem auto;
}

.profile-header {
  display: flex;
  align-items: center;
  gap: 2rem;
  margin-bottom: 2rem;
}

.profile-avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 3rem;
}

.profile-info {
  flex: 1;
}

.profile-info h1 {
  font-size: 2rem;
  margin-bottom: 0.5rem;
}

.profile-info p {
  color: rgba(255, 255, 255, 0.7);
  margin: 0.25rem 0;
}

.transaction-history h2 {
  margin-bottom: 2rem;
}

.no-orders {
  text-align: center;
  padding: 3rem;
}

.no-orders p {
  color: rgba(255, 255, 255, 0.6);
  margin-bottom: 1.5rem;
  font-size: 1.1rem;
}

.orders-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.order-item {
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 1.5rem;
}

.order-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.order-id {
  font-weight: 600;
  font-size: 1.1rem;
}

.order-date {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.9rem;
  margin-top: 0.25rem;
}

.order-amount {
  font-size: 1.5rem;
  font-weight: bold;
  color: #27ae60;
}

.order-details {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
}

.status-badge {
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  text-transform: uppercase;
}

.status-badge.processing {
  background: rgba(52, 152, 219, 0.2);
  color: #3498db;
}

.status-badge.completed {
  background: rgba(39, 174, 96, 0.2);
  color: #27ae60;
}

.status-badge.exited {
  background: rgba(149, 165, 166, 0.2);
  color: #95a5a6;
}

.order-items-count {
  color: rgba(255, 255, 255, 0.7);
}

.btn-sm {
  padding: 0.5rem 1rem;
  font-size: 0.9rem;
}
```
