import React, { useState, useEffect } from 'react';
import { ShoppingCart, Wallet, Package, Upload, FileText, Shield, Download, AlertCircle } from 'lucide-react';

const FilecoinEcommerce = () => {
  const [cart, setCart] = useState([]);
  const [showCart, setShowCart] = useState(false);
  const [walletConnected, setWalletConnected] = useState(false);
  const [walletAddress, setWalletAddress] = useState('');
  const [walletBalance, setWalletBalance] = useState('0');
  const [selectedCategory, setSelectedCategory] = useState('all');
  const [isConnecting, setIsConnecting] = useState(false);
  const [error, setError] = useState('');
  const [networkName, setNetworkName] = useState('');

  const products = [
    {
      id: 1,
      name: 'Decentralized Storage Plan - 100GB',
      price: 5.99,
      category: 'storage',
      image: '🗄️',
      description: 'Store your data securely on Filecoin network',
      filecoinPrice: '0.15 FIL'
    },
    {
      id: 2,
      name: 'NFT Marketplace Access',
      price: 29.99,
      category: 'digital',
      image: '🎨',
      description: 'Premium NFT trading platform subscription',
      filecoinPrice: '0.75 FIL'
    },
    {
      id: 3,
      name: 'Web3 Development Kit',
      price: 49.99,
      category: 'digital',
      image: '💻',
      description: 'Complete toolkit for building on Filecoin',
      filecoinPrice: '1.25 FIL'
    },
    {
      id: 4,
      name: 'Decentralized Storage - 1TB',
      price: 49.99,
      category: 'storage',
      image: '💾',
      description: 'Enterprise-grade Filecoin storage solution',
      filecoinPrice: '1.25 FIL'
    },
    {
      id: 5,
      name: 'IPFS Hosting Package',
      price: 19.99,
      category: 'hosting',
      image: '🌐',
      description: 'Host your website on IPFS via Filecoin',
      filecoinPrice: '0.50 FIL'
    },
    {
      id: 6,
      name: 'Smart Contract Templates',
      price: 39.99,
      category: 'digital',
      image: '📜',
      description: 'Pre-built smart contracts for Filecoin',
      filecoinPrice: '1.00 FIL'
    }
  ];

  const categories = [
    { id: 'all', name: 'All Products' },
    { id: 'storage', name: 'Storage' },
    { id: 'digital', name: 'Digital Assets' },
    { id: 'hosting', name: 'Hosting' }
  ];

  // Filecoin Mainnet Chain ID
  const FILECOIN_MAINNET_CHAIN_ID = '0x13a'; // 314 in decimal
  const FILECOIN_CALIBRATION_CHAIN_ID = '0x4cb2f'; // 314159 in decimal

  // Check if wallet is already connected on component mount
  useEffect(() => {
    checkIfWalletIsConnected();
    
    // Listen for account changes
    if (window.ethereum) {
      window.ethereum.on('accountsChanged', handleAccountsChanged);
      window.ethereum.on('chainChanged', handleChainChanged);
    }

    return () => {
      if (window.ethereum) {
        window.ethereum.removeListener('accountsChanged', handleAccountsChanged);
        window.ethereum.removeListener('chainChanged', handleChainChanged);
      }
    };
  }, []);

  const checkIfWalletIsConnected = async () => {
    try {
      if (!window.ethereum) return;

      const accounts = await window.ethereum.request({ method: 'eth_accounts' });
      
      if (accounts.length > 0) {
        setWalletAddress(accounts[0]);
        setWalletConnected(true);
        await getBalance(accounts[0]);
        await getNetworkName();
      }
    } catch (err) {
      console.error('Error checking wallet connection:', err);
    }
  };

  const handleAccountsChanged = (accounts) => {
    if (accounts.length === 0) {
      // User disconnected wallet
      disconnectWallet();
    } else {
      setWalletAddress(accounts[0]);
      getBalance(accounts[0]);
    }
  };

  const handleChainChanged = () => {
    // Reload the page when chain changes
    window.location.reload();
  };

  const getBalance = async (address) => {
    try {
      const balance = await window.ethereum.request({
        method: 'eth_getBalance',
        params: [address, 'latest']
      });
      
      // Convert from Wei to FIL (same as ETH conversion)
      const balanceInFIL = parseInt(balance, 16) / Math.pow(10, 18);
      setWalletBalance(balanceInFIL.toFixed(4));
    } catch (err) {
      console.error('Error getting balance:', err);
    }
  };

  const getNetworkName = async () => {
    try {
      const chainId = await window.ethereum.request({ method: 'eth_chainId' });
      
      if (chainId === FILECOIN_MAINNET_CHAIN_ID) {
        setNetworkName('Filecoin Mainnet');
      } else if (chainId === FILECOIN_CALIBRATION_CHAIN_ID) {
        setNetworkName('Filecoin Calibration');
      } else {
        setNetworkName('Unknown Network');
      }
    } catch (err) {
      console.error('Error getting network:', err);
    }
  };

  const switchToFilecoinNetwork = async () => {
    try {
      await window.ethereum.request({
        method: 'wallet_switchEthereumChain',
        params: [{ chainId: FILECOIN_MAINNET_CHAIN_ID }],
      });
      setNetworkName('Filecoin Mainnet');
    } catch (switchError) {
      // This error code indicates that the chain has not been added to MetaMask
      if (switchError.code === 4902) {
        try {
          await window.ethereum.request({
            method: 'wallet_addEthereumChain',
            params: [
              {
                chainId: FILECOIN_MAINNET_CHAIN_ID,
                chainName: 'Filecoin Mainnet',
                nativeCurrency: {
                  name: 'Filecoin',
                  symbol: 'FIL',
                  decimals: 18
                },
                rpcUrls: ['https://api.node.glif.io/rpc/v1'],
                blockExplorerUrls: ['https://filfox.info/en']
              }
            ],
          });
          setNetworkName('Filecoin Mainnet');
        } catch (addError) {
          setError('Failed to add Filecoin network');
          console.error('Error adding network:', addError);
        }
      } else {
        setError('Failed to switch network');
        console.error('Error switching network:', switchError);
      }
    }
  };

  const connectWallet = async () => {
    setIsConnecting(true);
    setError('');

    try {
      // Check if MetaMask is installed
      if (!window.ethereum) {
        setError('MetaMask is not installed. Please install MetaMask to continue.');
        setIsConnecting(false);
        return;
      }

      // Request account access
      const accounts = await window.ethereum.request({
        method: 'eth_requestAccounts'
      });

      if (accounts.length > 0) {
        setWalletAddress(accounts[0]);
        setWalletConnected(true);
        await getBalance(accounts[0]);
        await getNetworkName();
        
        // Optionally prompt to switch to Filecoin network
        const chainId = await window.ethereum.request({ method: 'eth_chainId' });
        if (chainId !== FILECOIN_MAINNET_CHAIN_ID && chainId !== FILECOIN_CALIBRATION_CHAIN_ID) {
          // Ask user if they want to switch to Filecoin
          const shouldSwitch = confirm('Would you like to switch to Filecoin Mainnet?');
          if (shouldSwitch) {
            await switchToFilecoinNetwork();
          }
        }
      }
    } catch (err) {
      if (err.code === 4001) {
        setError('Please connect to MetaMask.');
      } else {
        setError('An error occurred while connecting to MetaMask.');
      }
      console.error('Error connecting wallet:', err);
    } finally {
      setIsConnecting(false);
    }
  };

  const disconnectWallet = () => {
    setWalletConnected(false);
    setWalletAddress('');
    setWalletBalance('0');
    setNetworkName('');
  };

  const handleCheckout = async () => {
    if (!walletConnected) {
      setError('Please connect your wallet first');
      return;
    }

    const totalFIL = getTotalFIL();
    
    try {
      // Convert FIL to Wei (same as ETH conversion)
      const valueInWei = '0x' + Math.floor(parseFloat(totalFIL) * Math.pow(10, 18)).toString(16);
      
      // Transaction to your payment address
      const transactionParameters = {
        to: '0xeC96e791610605880Cf94EdEb0aaFbCc8Aba234E', // Your MetaMask payment address
        from: walletAddress,
        value: valueInWei,
        data: '0x' // You can encode purchase data here
      };

      const txHash = await window.ethereum.request({
        method: 'eth_sendTransaction',
        params: [transactionParameters],
      });

      alert(`Transaction sent! Hash: ${txHash}\n\nYour order will be processed once the transaction is confirmed.`);
      
      // Clear cart after successful transaction
      setCart([]);
    } catch (err) {
      if (err.code === 4001) {
        setError('Transaction was rejected');
      } else {
        setError('Transaction failed: ' + err.message);
      }
      console.error('Transaction error:', err);
    }
  };

  const addToCart = (product) => {
    const existing = cart.find(item => item.id === product.id);
    if (existing) {
      setCart(cart.map(item => 
        item.id === product.id ? { ...item, quantity: item.quantity + 1 } : item
      ));
    } else {
      setCart([...cart, { ...product, quantity: 1 }]);
    }
  };

  const removeFromCart = (productId) => {
    setCart(cart.filter(item => item.id !== productId));
  };

  const updateQuantity = (productId, change) => {
    setCart(cart.map(item => {
      if (item.id === productId) {
        const newQuantity = item.quantity + change;
        return newQuantity > 0 ? { ...item, quantity: newQuantity } : item;
      }
      return item;
    }).filter(item => item.quantity > 0));
  };

  const getTotalPrice = () => {
    return cart.reduce((sum, item) => sum + (item.price * item.quantity), 0).toFixed(2);
  };

  const getTotalFIL = () => {
    return cart.reduce((sum, item) => {
      const filPrice = parseFloat(item.filecoinPrice.split(' ')[0]);
      return sum + (filPrice * item.quantity);
    }, 0).toFixed(2);
  };

  const filteredProducts = selectedCategory === 'all' 
    ? products 
    : products.filter(p => p.category === selectedCategory);

  const formatAddress = (address) => {
    return `${address.substring(0, 6)}...${address.substring(address.length - 4)}`;
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 via-indigo-50 to-purple-50">
      {/* Header */}
      <header className="bg-white shadow-md sticky top-0 z-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between items-center py-4">
            <div className="flex items-center space-x-3">
              <div className="w-10 h-10 bg-gradient-to-br from-blue-600 to-purple-600 rounded-lg flex items-center justify-center">
                <Package className="w-6 h-6 text-white" />
              </div>
              <div>
                <h1 className="text-2xl font-bold bg-gradient-to-r from-blue-600 to-purple-600 bg-clip-text text-transparent">
                  FileCoin Market
                </h1>
                <p className="text-xs text-gray-500">Decentralized Commerce</p>
              </div>
            </div>
            
            <div className="flex items-center space-x-4">
              {walletConnected ? (
                <div className="flex items-center space-x-2">
                  <div className="hidden md:block text-right text-sm">
                    <div className="font-semibold text-gray-700">
                      {formatAddress(walletAddress)}
                    </div>
                    <div className="text-xs text-gray-500">
                      {walletBalance} FIL
                    </div>
                    {networkName && (
                      <div className="text-xs text-blue-600">
                        {networkName}
                      </div>
                    )}
                  </div>
                  <button
                    onClick={disconnectWallet}
                    className="flex items-center space-x-2 px-4 py-2 rounded-lg bg-green-100 text-green-700 border-2 border-green-300 hover:bg-green-200 transition-all"
                  >
                    <Wallet className="w-4 h-4" />
                    <span className="hidden sm:inline">Connected</span>
                  </button>
                </div>
              ) : (
                <button
                  onClick={connectWallet}
                  disabled={isConnecting}
                  className="flex items-center space-x-2 px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700 transition-all disabled:bg-gray-400 disabled:cursor-not-allowed"
                >
                  <Wallet className="w-4 h-4" />
                  <span className="hidden sm:inline">
                    {isConnecting ? 'Connecting...' : 'Connect Wallet'}
                  </span>
                </button>
              )}
              
              <button
                onClick={() => setShowCart(!showCart)}
                className="relative p-2 bg-gray-100 rounded-lg hover:bg-gray-200 transition-all"
              >
                <ShoppingCart className="w-6 h-6 text-gray-700" />
                {cart.length > 0 && (
                  <span className="absolute -top-2 -right-2 bg-red-500 text-white text-xs w-5 h-5 rounded-full flex items-center justify-center">
                    {cart.length}
                  </span>
                )}
              </button>
            </div>
          </div>
        </div>
      </header>

      {/* Error Alert */}
      {error && (
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-4">
          <div className="bg-red-50 border-l-4 border-red-500 p-4 rounded-lg flex items-start space-x-3">
            <AlertCircle className="w-5 h-5 text-red-500 flex-shrink-0 mt-0.5" />
            <div>
              <p className="text-red-800 font-semibold">Error</p>
              <p className="text-red-700 text-sm">{error}</p>
            </div>
            <button
              onClick={() => setError('')}
              className="ml-auto text-red-500 hover:text-red-700"
            >
              ✕
            </button>
          </div>
        </div>
      )}

      {/* Hero Section */}
      <div className="bg-gradient-to-r from-blue-600 via-indigo-600 to-purple-600 text-white py-16">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center">
            <h2 className="text-4xl md:text-5xl font-bold mb-4">
              Decentralized Commerce on Filecoin
            </h2>
            <p className="text-xl text-blue-100 mb-8 max-w-2xl mx-auto">
              Buy storage, digital assets, and services with cryptocurrency on the Filecoin network
            </p>
            <div className="flex flex-wrap justify-center gap-4">
              <div className="flex items-center space-x-2 bg-white/20 backdrop-blur-sm px-4 py-2 rounded-lg">
                <Shield className="w-5 h-5" />
                <span>Secure & Decentralized</span>
              </div>
              <div className="flex items-center space-x-2 bg-white/20 backdrop-blur-sm px-4 py-2 rounded-lg">
                <FileText className="w-5 h-5" />
                <span>Smart Contracts</span>
              </div>
              <div className="flex items-center space-x-2 bg-white/20 backdrop-blur-sm px-4 py-2 rounded-lg">
                <Upload className="w-5 h-5" />
                <span>IPFS Storage</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {/* Category Filter */}
        <div className="flex flex-wrap gap-2 mb-8">
          {categories.map(cat => (
            <button
              key={cat.id}
              onClick={() => setSelectedCategory(cat.id)}
              className={`px-4 py-2 rounded-lg transition-all ${
                selectedCategory === cat.id
                  ? 'bg-blue-600 text-white shadow-lg'
                  : 'bg-white text-gray-700 hover:bg-gray-100'
              }`}
            >
              {cat.name}
            </button>
          ))}
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
          {/* Products Grid */}
          <div className="lg:col-span-2">
            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              {filteredProducts.map(product => (
                <div
                  key={product.id}
                  className="bg-white rounded-xl shadow-lg overflow-hidden hover:shadow-2xl transition-all transform hover:-translate-y-1"
                >
                  <div className="bg-gradient-to-br from-blue-100 to-purple-100 h-48 flex items-center justify-center text-6xl">
                    {product.image}
                  </div>
                  <div className="p-6">
                    <h3 className="text-xl font-bold text-gray-800 mb-2">
                      {product.name}
                    </h3>
                    <p className="text-gray-600 text-sm mb-4">
                      {product.description}
                    </p>
                    <div className="flex items-center justify-between mb-4">
                      <div>
                        <p className="text-2xl font-bold text-blue-600">
                          ${product.price}
                        </p>
                        <p className="text-sm text-purple-600 font-semibold">
                          {product.filecoinPrice}
                        </p>
                      </div>
                      <button
                        onClick={() => addToCart(product)}
                        className="bg-gradient-to-r from-blue-600 to-purple-600 text-white px-6 py-2 rounded-lg hover:from-blue-700 hover:to-purple-700 transition-all font-semibold"
                      >
                        Add to Cart
                      </button>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>

          {/* Cart Sidebar */}
          <div className="lg:col-span-1">
            <div className="bg-white rounded-xl shadow-lg p-6 sticky top-24">
              <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <ShoppingCart className="w-6 h-6 mr-2 text-blue-600" />
                Shopping Cart
              </h2>
              
              {cart.length === 0 ? (
                <div className="text-center py-8">
                  <ShoppingCart className="w-16 h-16 text-gray-300 mx-auto mb-4" />
                  <p className="text-gray-500">Your cart is empty</p>
                </div>
              ) : (
                <>
                  <div className="space-y-4 mb-6 max-h-96 overflow-y-auto">
                    {cart.map(item => (
                      <div key={item.id} className="border-b pb-4">
                        <div className="flex justify-between items-start mb-2">
                          <div className="flex-1">
                            <h4 className="font-semibold text-gray-800 text-sm">
                              {item.name}
                            </h4>
                            <p className="text-blue-600 font-semibold">
                              ${item.price}
                            </p>
                            <p className="text-xs text-purple-600">
                              {item.filecoinPrice}
                            </p>
                          </div>
                          <button
                            onClick={() => removeFromCart(item.id)}
                            className="text-red-500 hover:text-red-700 text-sm"
                          >
                            Remove
                          </button>
                        </div>
                        <div className="flex items-center space-x-2">
                          <button
                            onClick={() => updateQuantity(item.id, -1)}
                            className="w-8 h-8 bg-gray-200 rounded hover:bg-gray-300"
                          >
                            -
                          </button>
                          <span className="w-8 text-center font-semibold">
                            {item.quantity}
                          </span>
                          <button
                            onClick={() => updateQuantity(item.id, 1)}
                            className="w-8 h-8 bg-gray-200 rounded hover:bg-gray-300"
                          >
                            +
                          </button>
                        </div>
                      </div>
                    ))}
                  </div>
                  
                  <div className="border-t pt-4 space-y-2">
                    <div className="flex justify-between text-lg font-semibold">
                      <span>Total (USD):</span>
                      <span className="text-blue-600">${getTotalPrice()}</span>
                    </div>
                    <div className="flex justify-between text-lg font-semibold">
                      <span>Total (FIL):</span>
                      <span className="text-purple-600">{getTotalFIL()} FIL</span>
                    </div>
                  </div>
                  
                  <button
                    onClick={handleCheckout}
                    disabled={!walletConnected}
                    className={`w-full mt-6 py-3 rounded-lg font-semibold transition-all ${
                      walletConnected
                        ? 'bg-gradient-to-r from-blue-600 to-purple-600 text-white hover:from-blue-700 hover:to-purple-700'
                        : 'bg-gray-300 text-gray-500 cursor-not-allowed'
                    }`}
                  >
                    {walletConnected ? 'Checkout with Filecoin' : 'Connect Wallet to Checkout'}
                  </button>
                  
                  {!walletConnected && (
                    <p className="text-xs text-center text-gray-500 mt-2">
                      Connect MetaMask to complete your purchase
                    </p>
                  )}
                </>
              )}
            </div>
          </div>
        </div>
      </div>

      {/* Footer */}
      <footer className="bg-gray-900 text-white mt-16 py-8">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
          <p className="text-gray-400">
            © 2025 FileCoin Market - Decentralized E-Commerce Platform
          </p>
          <p className="text-sm text-gray-500 mt-2">
            Built on Filecoin Network | Powered by IPFS | Secured by MetaMask
          </p>
        </div>
      </footer>
    </div>
  );
};

export default FilecoinEcommerce;
