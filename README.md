# Cairos

This is a daily scheduler that allows you to have 15 minute interval plans, and gives you a summary on your performance. This project is uses Vue3 with Vite and Tailwind CSS, and vibe-coded using Claude Sonnet 4.

![](docs/example.png)

## Getting Started

```bash
# Web - Docker deployment
# Build the image
docker build -t cairos .

# Run the container
docker run -p 8080:80 cairos

# Mobile App Development
# Install dependencies
npm install

# Build the web assets
npm run build

# Add mobile platforms
npx cap add android
npx cap add ios

# Open native IDEs
npx cap open android  # Opens Android Studio
npx cap open ios     # Opens Xcode (macOS only)

# Sync web code with native projects
npx cap sync

# Live development
npm run dev
npx cap run android  # Run on Android device/emulator
npx cap run ios      # Run on iOS simulator (macOS only)
```

### Requirements for Mobile Development

#### Android
- Android Studio
- Java Development Kit (JDK) 17 or newer
- Android SDK
- Android device or emulator

#### iOS (macOS only)
- Xcode 15 or newer
- iOS device or simulator
- macOS computer
- Xcode Command Line Tools
