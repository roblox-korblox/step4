# step4

import 'package:flutter/material.dart';

class Step4 extends StatefulWidget {
  const Step4({super.key});

  @override
  State<Step4> createState() => _Step4State();
}

class _Step4State extends State<Step4> {

  final List<Product> products = const [
    Product(
      title: "GTA VI Early Access",
      description:
          "The next major entry in Rockstar Games open-world action series.",
      priceText: "Rp 1.200.000",
      priceValue: 1200000,
      imagePath: "assets/gtavi.jpg",
    ),
    Product(
      title: "NFS Heat",
      description:
          "You compete in the Speedhunter Showdown, legal street races.",
      priceText: "Rp 759.000",
      priceValue: 759000,
      imagePath: "assets/nfsheat.jpg",
    ),
    Product(
      title: "WRC 10 (World Rally Championship)",
      description:
          "Official rally racing video game based on the 2021 WRC season.",
      priceText: "Rp 424.999",
      priceValue: 424999,
      imagePath: "assets/wrc10.jpeg",
    ),
  ];

  final List<CartItem> cart = [];

  void addToCart(Product product) {
    setState(() {
      final idx = cart.indexWhere((c) => c.product.title == product.title);
      if (idx >= 0) {
        cart[idx] = cart[idx].copyWith(qty: cart[idx].qty + 1);
      } else {
        cart.add(CartItem(product: product, qty: 1));
      }
    });

    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text("${product.title} ditambahkan ke cart"),
        duration: const Duration(seconds: 1),
      ),
    );
  }

  void openCart() async {

    final updated = await Navigator.push<List<CartItem>>(
      context,
      MaterialPageRoute(
        builder: (_) => CartPage(
          cart: List<CartItem>.from(cart),
        ),
      ),
    );

    if (updated != null) {
      setState(() {
        cart
          ..clear()
          ..addAll(updated);
      });
    }
  }

  int get cartCount => cart.fold(0, (sum, item) => sum + item.qty);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("GameForged Hub"),
        backgroundColor: Colors.white,
        foregroundColor: Colors.black,
        elevation: 0,
        actions: [
          Stack(
            alignment: Alignment.center,
            children: [
              IconButton(
                onPressed: openCart,
                icon: const Icon(Icons.shopping_cart_outlined),
              ),
              if (cartCount > 0)
                Positioned(
                  right: 8,
                  top: 10,
                  child: Container(
                    padding: const EdgeInsets.symmetric(
                      horizontal: 6,
                      vertical: 2,
                    ),
                    decoration: BoxDecoration(
                      color: Colors.red,
                      borderRadius: BorderRadius.circular(12),
                    ),
                    child: Text(
                      "$cartCount",
                      style: const TextStyle(
                        color: Colors.white,
                        fontSize: 12,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                  ),
                ),
            ],
          ),
        ],
      ),
      backgroundColor: const Color(0xFFF2F2F2),
      body: ListView.builder(
        padding: const EdgeInsets.all(16),
        itemCount: products.length,
        itemBuilder: (context, i) {
          final p = products[i];
          return ProductCard(
            product: p,
            onBuy: () => addToCart(p),
            onTap: () async {
            
              final wantAdd = await Navigator.push<bool>(
                context,
                MaterialPageRoute(
                  builder: (_) => ProductDetailPage(product: p),
                ),
              );

              if (wantAdd == true) addToCart(p);
            },
          );
        },
      ),
    );
  }
}



class Product {
  final String title;
  final String description;
  final String priceText;
  final int priceValue;
  final String imagePath;

  const Product({
    required this.title,
    required this.description,
    required this.priceText,
    required this.priceValue,
    required this.imagePath,
  });
}

class CartItem {
  final Product product;
  final int qty;

  const CartItem({required this.product, required this.qty});

  CartItem copyWith({Product? product, int? qty}) {
    return CartItem(
      product: product ?? this.product,
      qty: qty ?? this.qty,
    );
  }
}



class ProductCard extends StatelessWidget {
  final Product product;
  final VoidCallback onBuy;
  final VoidCallback onTap;

  const ProductCard({
    super.key,
    required this.product,
    required this.onBuy,
    required this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.only(bottom: 16),
      elevation: 4,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(16),
      ),
      child: InkWell(
        borderRadius: BorderRadius.circular(16),
        onTap: onTap,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            
            ClipRRect(
              borderRadius: const BorderRadius.vertical(
                top: Radius.circular(16),
              ),
              child: SizedBox(
                height: 160,
                width: double.infinity,
                child: Image.asset(
                  product.imagePath,
                  fit: BoxFit.cover,
                  errorBuilder: (context, error, stackTrace) {
                    return Container(
                      color: Colors.grey.shade300,
                      child: const Icon(Icons.broken_image, size: 50),
                    );
                  },
                ),
              ),
            ),

            Padding(
              padding: const EdgeInsets.all(16),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    product.title,
                    maxLines: 1,
                    overflow: TextOverflow.ellipsis,
                    style: const TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  const SizedBox(height: 6),
                  Text(
                    product.description,
                    maxLines: 2,
                    overflow: TextOverflow.ellipsis,
                    style: TextStyle(
                      color: Colors.grey.shade600,
                      height: 1.25,
                    ),
                  ),
                  const SizedBox(height: 12),

                  Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: [
                      Text(
                        product.priceText,
                        style: const TextStyle(
                          fontSize: 16,
                          fontWeight: FontWeight.bold,
                          color: Colors.green,
                        ),
                      ),
                      ElevatedButton(
                        onPressed: onBuy,
                        style: ElevatedButton.styleFrom(
                          backgroundColor: Colors.white,
                          foregroundColor: Colors.black,
                          elevation: 1,
                          shape: RoundedRectangleBorder(
                            borderRadius: BorderRadius.circular(10),
                          ),
                        ),
                        child: const Text("Add to Cart"),
                      ),
                    ],
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}



class ProductDetailPage extends StatelessWidget {
  final Product product;

  const ProductDetailPage({super.key, required this.product});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(product.title),
      ),
      body: ListView(
        children: [
          SizedBox(
            height: 260,
            width: double.infinity,
            child: Image.asset(
              product.imagePath,
              fit: BoxFit.cover,
              errorBuilder: (context, error, stackTrace) {
                return Container(
                  color: Colors.grey.shade300,
                  child: const Icon(Icons.broken_image, size: 60),
                );
              },
            ),
          ),
          Padding(
            padding: const EdgeInsets.all(16),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  product.title,
                  style: const TextStyle(
                    fontSize: 20,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(height: 8),
                Text(
                  product.description,
                  style: TextStyle(
                    color: Colors.grey.shade700,
                    height: 1.35,
                    fontSize: 14,
                  ),
                ),
                const SizedBox(height: 16),
                Text(
                  product.priceText,
                  style: const TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.bold,
                    color: Colors.green,
                  ),
                ),
                const SizedBox(height: 18),
                SizedBox(
                  width: double.infinity,
                  height: 48,
                  child: ElevatedButton(
                    onPressed: () {
                      Navigator.pop(context, true); 
                    },
                    child: const Text("Add to Cart"),
                  ),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}



class CartPage extends StatefulWidget {
  final List<CartItem> cart;

  const CartPage({super.key, required this.cart});

  @override
  State<CartPage> createState() => _CartPageState();
}

class _CartPageState extends State<CartPage> {
  late List<CartItem> cart;

  @override
  void initState() {
    super.initState();
    cart = widget.cart;
  }

  void inc(int index) {
    setState(() {
      cart[index] = cart[index].copyWith(qty: cart[index].qty + 1);
    });
  }

  void dec(int index) {
    setState(() {
      final current = cart[index].qty;
      if (current <= 1) {
        cart.removeAt(index);
      } else {
        cart[index] = cart[index].copyWith(qty: current - 1);
      }
    });
  }

  int get total => cart.fold(0, (sum, item) => sum + item.product.priceValue * item.qty);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Cart"),
        leading: IconButton(
          icon: const Icon(Icons.arrow_back),
          onPressed: () => Navigator.pop(context, cart),
        ),
      ),
      body: cart.isEmpty
          ? const Center(child: Text("Cart masih kosong"))
          : Column(
              children: [
                Expanded(
                  child: ListView.separated(
                    padding: const EdgeInsets.all(16),
                    itemCount: cart.length,
                    separatorBuilder: (_, __) => const SizedBox(height: 12),
                    itemBuilder: (context, i) {
                      final item = cart[i];
                      return Card(
                        child: Padding(
                          padding: const EdgeInsets.all(12),
                          child: Row(
                            children: [
                              ClipRRect(
                                borderRadius: BorderRadius.circular(10),
                                child: SizedBox(
                                  width: 70,
                                  height: 70,
                                  child: Image.asset(
                                    item.product.imagePath,
                                    fit: BoxFit.cover,
                                    errorBuilder: (c, e, s) => Container(
                                      color: Colors.grey.shade300,
                                      child: const Icon(Icons.broken_image),
                                    ),
                                  ),
                                ),
                              ),
                              const SizedBox(width: 12),
                              Expanded(
                                child: Column(
                                  crossAxisAlignment: CrossAxisAlignment.start,
                                  children: [
                                    Text(
                                      item.product.title,
                                      maxLines: 1,
                                      overflow: TextOverflow.ellipsis,
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                    const SizedBox(height: 6),
                                    Text(
                                      item.product.priceText,
                                      style: const TextStyle(
                                        color: Colors.green,
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                  ],
                                ),
                              ),
                              Row(
                                children: [
                                  IconButton(
                                    onPressed: () => dec(i),
                                    icon: const Icon(Icons.remove_circle_outline),
                                  ),
                                  Text(
                                    "${item.qty}",
                                    style: const TextStyle(
                                      fontWeight: FontWeight.bold,
                                    ),
                                  ),
                                  IconButton(
                                    onPressed: () => inc(i),
                                    icon: const Icon(Icons.add_circle_outline),
                                  ),
                                ],
                              ),
                            ],
                          ),
                        ),
                      );
                    },
                  ),
                ),
                Container(
                  padding: const EdgeInsets.all(16),
                  decoration: BoxDecoration(
                    color: Colors.white,
                    boxShadow: [
                      BoxShadow(
                        blurRadius: 10,
                        color: Colors.black.withOpacity(0.08),
                        offset: const Offset(0, -2),
                      )
                    ],
                  ),
                  child: Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: [
                      const Text(
                        "Total",
                        style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                      ),
                      Text(
                        "Rp ${total.toString()}",
                        style: const TextStyle(
                          fontSize: 16,
                          fontWeight: FontWeight.bold,
                          color: Colors.green,
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
    );
  }
}
