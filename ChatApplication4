import java.awt.Desktop;
import java.io.File;
import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.Scanner;

public class ChatApplication4 {

    // User class
    public static class User {
        private String username;
        private List<Notification> notifications;
        private boolean isOnline;

        public User(String username) {
            this.username = username;
            this.notifications = new ArrayList<>();
            this.isOnline = true;
        }

        public String getUsername() {
            return username;
        }

        public boolean isOnline() {
            return isOnline;
        }

        public void setOnline(boolean online) {
            isOnline = online;
        }

        public void receiveNotification(Notification notification) {
            notifications.add(notification);
            if (isOnline) {
                System.out.println(username + " received notification: " + notification.getMessage());
                handleNotification(notification);
            } else {
                System.out.println(username + " received notification while offline: " + notification.getMessage());
            }
        }

        public List<Notification> getNotifications() {
            return notifications;
        }

        private void handleNotification(Notification notification) {
            Scanner scanner = new Scanner(System.in);
            if (notification.getImagePath() != null) {
                System.out.println("Notification contains an image. Do you want to open it? (yes/no)");
                String response = scanner.nextLine();
                if (response.equalsIgnoreCase("yes")) {
                    openLink(notification.getImagePath());
                }
            }
            if (notification.getVideoPath() != null) {
                System.out.println("Notification contains a video. Do you want to open it? (yes/no)");
                String response = scanner.nextLine();
                if (response.equalsIgnoreCase("yes")) {
                    openLink(notification.getVideoPath());
                }
            }
        }

        private void openLink(String link) {
            if (Desktop.isDesktopSupported()) {
                try {
                    if (link != null) {
                        if (link.startsWith("http://") || link.startsWith("https://")) {
                            Desktop.getDesktop().browse(new URI(link));
                        } else {
                            Desktop.getDesktop().open(new File(link));
                        }
                    }
                } catch (IOException | URISyntaxException ex) {
                    ex.printStackTrace();
                }
            }
        }
    }

    // Message class with Builder pattern
    public static class Message {
        private String text;
        private String imagePath;
        private String videoPath;

        private Message(MessageBuilder builder) {
            this.text = builder.text;
            this.imagePath = builder.imagePath;
            this.videoPath = builder.videoPath;
        }

        public String getText() {
            return text;
        }

        public String getImagePath() {
            return imagePath;
        }

        public String getVideoPath() {
            return videoPath;
        }

        public static class MessageBuilder {
            private String text;
            private String imagePath;
            private String videoPath;

            public MessageBuilder setText(String text) {
                this.text = text;
                return this;
            }

            public MessageBuilder setImagePath(String imagePath) {
                this.imagePath = imagePath;
                return this;
            }

            public MessageBuilder setVideoPath(String videoPath) {
                this.videoPath = videoPath;
                return this;
            }

            public Message build() {
                return new Message(this);
            }
        }
    }

    // Notification class
    public static class Notification {
        private String message;
        private String imagePath;
        private String videoPath;

        public Notification(String message, String imagePath, String videoPath) {
            this.message = message;
            this.imagePath = imagePath;
            this.videoPath = videoPath;
        }

        public String getMessage() {
            return message;
        }

        public String getImagePath() {
            return imagePath;
        }

        public String getVideoPath() {
            return videoPath;
        }
    }

    // ChatRoom class with Mediator and Observer pattern
    public static class ChatRoom {
        private List<User> users;
        private List<User> offlineUsers;

        public ChatRoom() {
            this.users = new ArrayList<>();
            this.offlineUsers = new ArrayList<>();
        }

        public void join(User user) {
            users.add(user);
            user.setOnline(true);
            notifyUser(user, new Notification("You have joined the chat.", null, null));
        }

        public void leave(User user) {
            user.setOnline(false);
            users.remove(user);
            offlineUsers.add(user);
            notifyUser(user, new Notification("You have left the chat.", null, null));
        }

        public void sendMessage(User sender, String recipientUsername, Message message) {
            Optional<User> recipientOpt = users.stream()
                    .filter(user -> user.getUsername().equals(recipientUsername))
                    .findFirst();

            if (!recipientOpt.isPresent()) {
                recipientOpt = offlineUsers.stream()
                        .filter(user -> user.getUsername().equals(recipientUsername))
                        .findFirst();
            }

            if (recipientOpt.isPresent()) {
                User recipient = recipientOpt.get();
                String content = message.getText();
                String imagePath = null;
                String videoPath = null;
                if (message.getImagePath() != null) {
                    content += " [Image: " + message.getImagePath() + "]";
                    imagePath = message.getImagePath();
                }
                if (message.getVideoPath() != null) {
                    content += " [Video: " + message.getVideoPath() + "]";
                    videoPath = message.getVideoPath();
                }
                notifyUser(recipient, new Notification(sender.getUsername() + " sent a message: " + content, imagePath, videoPath));
            } else {
                System.out.println("Recipient not found.");
            }
        }

        private void notifyUser(User user, Notification notification) {
            user.receiveNotification(notification);
        }
    }

    // Main method 
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        ChatRoom chatRoom = new ChatRoom();

        System.out.println("Enter the number of users to add:");
        int numberOfUsers = Integer.parseInt(scanner.nextLine());

        List<User> users = new ArrayList<>();
        for (int i = 0; i < numberOfUsers; i++) {
            System.out.println("Enter username for user " + (i + 1) + ":");
            String username = scanner.nextLine();
            User user = new User(username);
            users.add(user);
            chatRoom.join(user);
        }

        while (true) {
            System.out.println("Choose an option: 1. Send message 2. Leave chat 3. Exit");
            String optionInput = scanner.nextLine();
            
            if (optionInput.equalsIgnoreCase("exit")) {
                break;
            }

            int option;
            try {
                option = Integer.parseInt(optionInput);
            } catch (NumberFormatException e) {
                System.out.println("Invalid option. Please enter a number.");
                continue;
            }

            if (option == 1) {
                System.out.println("Enter your username:");
                String senderUsername = scanner.nextLine();
                User sender = users.stream()
                        .filter(user -> user.getUsername().equals(senderUsername))
                        .findFirst()
                        .orElse(null);

                if (sender == null) {
                    System.out.println("User not found.");
                    continue;
                }

                System.out.println("Enter recipient username:");
                String recipientUsername = scanner.nextLine();

                Message.MessageBuilder messageBuilder = new Message.MessageBuilder();
                System.out.println("Enter message text:");
                String text = scanner.nextLine();
                messageBuilder.setText(text);

                System.out.println("Enter image path (or leave blank):");
                String imagePath = scanner.nextLine();
                if (!imagePath.isEmpty()) {
                    messageBuilder.setImagePath(imagePath);
                }

                System.out.println("Enter video path (or leave blank):");
                String videoPath = scanner.nextLine();
                if (!videoPath.isEmpty()) {
                    messageBuilder.setVideoPath(videoPath);
                }

                Message message = messageBuilder.build();
                chatRoom.sendMessage(sender, recipientUsername, message);
            } else if (option == 2) {
                System.out.println("Enter your username:");
                String username = scanner.nextLine();
                User user = users.stream()
                        .filter(u -> u.getUsername().equals(username))
                        .findFirst()
                        .orElse(null);

                if (user != null) {
                    chatRoom.leave(user);
                    users.remove(user);
                } else {
                    System.out.println("User not found.");
                }
            } else if (option == 3) {
                break;
            } else {
                System.out.println("Invalid option. Please try again.");
            }
        }

        scanner.close();
    }
}
