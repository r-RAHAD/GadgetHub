import java.util.*;

class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }
}

class Product {
    private int id;
    private String name;
    private double price;
    private int stock;
    private List<String> reviews;

    public Product(int id, String name, double price, int stock) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.stock = stock;
        this.reviews = new ArrayList<>();
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    public int getStock() {
        return stock;
    }

    public void setStock(int stock) {
        this.stock = stock;
    }

    public void addReview(String review) {
        reviews.add(review);
    }

    public List<String> getReviews() {
        return reviews;
    }

    @Override
    public String toString() {
        return "\u001B[94m[ID: " + id + "] " + name + " - $" + price + " (In Stock: " + stock + ")\u001B[0m";
    }
}

class CartItem {
    private Product product;
    private int quantity;

    public CartItem(Product product, int quantity) {
        this.product = product;
        this.quantity = quantity;
    }

    public Product getProduct() {
        return product;
    }

    public int getQuantity() {
        return quantity;
    }

    public double getTotalPrice() {
        return product.getPrice() * quantity;
    }

    @Override
    public String toString() {
        return product.getName() + " x " + quantity + " = $" + String.format("%.2f", getTotalPrice());
    }
}

class Cart {
    private List<CartItem> items;

    public Cart() {
        items = new ArrayList<>();
    }

    public void addProduct(Product product, int quantity) {
        if (product.getStock() < quantity) {
            System.out.println("\u001B[94mNot enough stock for: " + product.getName() + "\u001B[0m");
            return;
        }
        product.setStock(product.getStock() - quantity);
        items.add(new CartItem(product, quantity));
        System.out.println("\u001B[94mAdded to cart: " + quantity + " x " + product.getName() + "\u001B[0m");
    }

    public double getTotal() {
        return items.stream().mapToDouble(CartItem::getTotalPrice).sum();
    }

    public void displayCart() {
        if (items.isEmpty()) {
            System.out.println("\u001B[94mYour cart is empty.\u001B[0m");
            return;
        }
        System.out.println("\u001B[94mItems in Your Cart:\u001B[0m");
        items.forEach(System.out::println);
        System.out.println("\u001B[94mTotal: $" + String.format("%.2f", getTotal()) + "\u001B[0m");
    }

    public void clearCart() {
        items.clear();
    }
}

public class GadgetHub {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        List<User> users = new ArrayList<>();
        List<Product> products = new ArrayList<>();
        Cart cart = new Cart();
        User currentUser = null;

        // Product catalog
        products.add(new Product(1, "UltraBook Pro", 999.99, 15));
        products.add(new Product(2, "SmartCam 4K", 459.50, 10));
        products.add(new Product(3, "Gaming Headset", 79.99, 40));
        products.add(new Product(4, "Mechanical Keyboard", 110.00, 20));
        products.add(new Product(5, "Wireless Mouse", 35.99, 50));
        products.add(new Product(6, "Curved Monitor 27\"", 219.00, 12));
        products.add(new Product(7, "LaserJet Printer", 145.00, 8));
        products.add(new Product(8, "Android Tablet 10\"", 249.99, 18));
        products.add(new Product(9, "Dual-Band Router", 89.00, 25));
        products.add(new Product(10, "1TB SSD External", 129.99, 30));

        // Welcome message
        System.out.println("\u001B[94m==================================");
        System.out.println("       WELCOME TO GadgetHub     ");
        System.out.println("==================================");
        System.out.println("Your one-stop shop for electronics!\u001B[0m");

        while (true) {
            if (currentUser == null) {
                System.out.println("\n1. Register\n2. Login\n3. Exit");
                System.out.print("Choose an option: ");
                int choice = scanner.nextInt();
                scanner.nextLine();

                switch (choice) {
                    case 1:
                        System.out.print("Create a username: ");
                        String username = scanner.nextLine();
                        System.out.print("Create a password: ");
                        String password = scanner.nextLine();
                        users.add(new User(username, password));
                        System.out.println("\u001B[94mRegistration successful!\u001B[0m");
                        break;
                    case 2:
                        System.out.print("Username: ");
                        String loginUsername = scanner.nextLine();
                        System.out.print("Password: ");
                        String loginPassword = scanner.nextLine();
                        Optional<User> user = users.stream()
                            .filter(u -> u.getUsername().equals(loginUsername) && u.getPassword().equals(loginPassword))
                            .findFirst();
                        if (user.isPresent()) {
                            currentUser = user.get();
                            System.out.println("\u001B[94mLogin successful! Welcome, " + currentUser.getUsername() + "!\u001B[0m");
                        } else {
                            System.out.println("\u001B[94mInvalid credentials. Please try again.\u001B[0m");
                        }
                        break;
                    case 3:
                        System.out.println("\u001B[94mThanks for visiting GadgetHub!\u001B[0m");
                        return;
                    default:
                        System.out.println("\u001B[94mInvalid option. Please try again.\u001B[0m");
                }
            } else {
                System.out.println("\n1. Browse Products\n2. Add to Cart\n3. View Cart\n4. Product Reviews\n5. Checkout\n6. Logout\n7. Exit");
                System.out.print("Choose an option: ");
                int choice = scanner.nextInt();
                scanner.nextLine();

                switch (choice) {
                    case 1:
                        System.out.println("\u001B[94m\nProduct Catalog:\u001B[0m");
                        products.forEach(System.out::println);
                        break;
                    case 2:
                        System.out.print("Enter Product ID: ");
                        int productId = scanner.nextInt();
                        System.out.print("Enter quantity: ");
                        int quantity = scanner.nextInt();
                        scanner.nextLine();
                        Optional<Product> product = products.stream().filter(p -> p.getId() == productId).findFirst();
                        if (product.isPresent()) {
                            cart.addProduct(product.get(), quantity);
                        } else {
                            System.out.println("\u001B[94mProduct not found.\u001B[0m");
                        }
                        break;
                    case 3:
                        cart.displayCart();
                        break;
                    case 4:
                        System.out.print("Enter Product ID for reviews: ");
                        int reviewProductId = scanner.nextInt();
                        scanner.nextLine();
                        Optional<Product> reviewProduct = products.stream().filter(p -> p.getId() == reviewProductId).findFirst();
                        if (reviewProduct.isPresent()) {
                            System.out.println("\n1. Add Review\n2. View Reviews");
                            System.out.print("Choose an option: ");
                            int reviewChoice = scanner.nextInt();
                            scanner.nextLine();
                            if (reviewChoice == 1) {
                                System.out.print("Write your review: ");
                                String review = scanner.nextLine();
                                reviewProduct.get().addReview(review);
                                System.out.println("\u001B[94mReview added!\u001B[0m");
                            } else if (reviewChoice == 2) {
                                List<String> reviews = reviewProduct.get().getReviews();
                                if (reviews.isEmpty()) {
                                    System.out.println("\u001B[94mNo reviews yet for " + reviewProduct.get().getName() + "\u001B[0m");
                                } else {
                                    System.out.println("\u001B[94mReviews for " + reviewProduct.get().getName() + ":");
                                    reviews.forEach(r -> System.out.println("- " + r));
                                }
                            } else {
                                System.out.println("\u001B[94mInvalid option.\u001B[0m");
                            }
                        } else {
                            System.out.println("\u001B[94mInvalid Product ID.\u001B[0m");
                        }
                        break;
                    case 5:
                        System.out.println("\n\u001B[94mTotal Amount: $" + String.format("%.2f", cart.getTotal()) + "\u001B[0m");
                        System.out.println("Choose Payment Method:\n1. Credit Card\n2. Debit Card");
                        System.out.print("Select: ");
                        int paymentChoice = scanner.nextInt();
                        scanner.nextLine();
                        if (paymentChoice == 1 || paymentChoice == 2) {
                            System.out.println("\u001B[94mPayment Successful! Thank you for shopping with us.\u001B[0m");
                            cart.clearCart();
                        } else {
                            System.out.println("\u001B[94mInvalid payment option.\u001B[0m");
                        }
                        break;
                    case 6:
                        currentUser = null;
                        System.out.println("\u001B[94mYou have logged out.\u001B[0m");
                        break;
                    case 7:
                        System.out.println("\u001B[94mThank you for visiting GadgetHub!\u001B[0m");
                        return;
                    default:
                        System.out.println("\u001B[94mInvalid option. Try again.\u001B[0m");
                }
            }
       
        }
    }   
}     
