---
title: Durn Bus Away - Transit Information Display
date: 2023-08-20 16:00:00 -0800
categories: [Projects, Web Development]
tags: [javascript, electron, apis, raspberry-pi, public-transit, iot]
image:
  path: /assets/img/projects/transit-display.png
  alt: Transit Information Display System
---

# Durn Bus Away: Real-Time Transit Information Display

## Project Overview

**Durn Bus Away** is an Electron-based application that presents real-time transit information from the OneBusAway API, specifically designed for deployment on small devices like Raspberry Pi. This project addresses the need for accessible, community-focused transit information displays in public spaces.

**🔗 [View on GitHub](https://github.com/KyleODore/OneBusAwayStop-Electron)**

## The Problem

Public transit accessibility remains a challenge in many communities:
- **Limited real-time information** at transit stops
- **Expensive commercial solutions** for transit displays
- **Lack of customizable displays** for community needs
- **Digital divide** affecting transit information access
- **Need for low-cost, deployable solutions** for public spaces

## The Solution

Durn Bus Away provides a lightweight, cost-effective transit information system:

### 🚌 **Real-Time Transit Data**
- **OneBusAway API integration** for accurate arrival predictions
- **Multi-route support** for complex transit stops
- **Real-time updates** with automatic refresh capabilities
- **Offline graceful degradation** when connectivity is limited

### 💻 **Cross-Platform Deployment**
- **Electron framework** for cross-platform compatibility
- **Raspberry Pi optimization** for embedded deployment
- **Kiosk mode support** for public display installations
- **Low resource usage** for 24/7 operation

### 🎨 **Public Display Optimization**
- **High contrast interface** for outdoor visibility
- **Large, readable fonts** for accessibility
- **Clean, minimalist design** reducing visual clutter
- **Configurable layouts** for different screen sizes

## Technical Implementation

### **Architecture Overview**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Electron App  │    │  OneBusAway API │    │  Local Storage  │
│   (Renderer)    │────│   (External)    │────│   (Fallback)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │
┌─────────────────┐    ┌─────────────────┐
│  Display Logic  │    │  Error Handling │
│  (Main Process) │────│  & Fallback     │
└─────────────────┘    └─────────────────┘
```

### **Technology Stack**
- **Framework**: Electron for cross-platform desktop application
- **Frontend**: HTML5, CSS3, vanilla JavaScript
- **API Integration**: OneBusAway real-time transit API
- **Target Hardware**: Raspberry Pi 3/4, standard desktop computers
- **Development**: Node.js ecosystem with npm package management

### **Core Implementation**

#### **1. API Integration**
```javascript
class TransitDataService {
    constructor(apiKey, stopId) {
        this.apiKey = apiKey;
        this.stopId = stopId;
        this.baseUrl = 'https://api.pugetsound.onebusaway.org/api/where';
    }
    
    async getArrivals() {
        try {
            const response = await fetch(
                `${this.baseUrl}/arrivals-and-departures-for-stop/${this.stopId}.json?key=${this.apiKey}`
            );
            const data = await response.json();
            return this.processArrivals(data);
        } catch (error) {
            return this.handleApiError(error);
        }
    }
    
    processArrivals(data) {
        return data.data.entry.arrivalsAndDepartures
            .filter(arrival => arrival.predicted)
            .sort((a, b) => a.predictedArrivalTime - b.predictedArrivalTime)
            .slice(0, 10); // Show next 10 arrivals
    }
}
```

#### **2. Display Management**
```javascript
class DisplayController {
    constructor() {
        this.refreshInterval = 30000; // 30 seconds
        this.transitService = new TransitDataService(API_KEY, STOP_ID);
        this.initializeDisplay();
    }
    
    async updateDisplay() {
        const arrivals = await this.transitService.getArrivals();
        this.renderArrivals(arrivals);
        this.scheduleNextUpdate();
    }
    
    renderArrivals(arrivals) {
        const container = document.getElementById('arrivals-container');
        container.innerHTML = arrivals.map(arrival => 
            this.createArrivalElement(arrival)
        ).join('');
    }
    
    createArrivalElement(arrival) {
        const minutesUntil = Math.max(0, 
            Math.floor((arrival.predictedArrivalTime - Date.now()) / 60000)
        );
        
        return `
            <div class="arrival-item">
                <span class="route-number">${arrival.routeShortName}</span>
                <span class="route-destination">${arrival.tripHeadsign}</span>
                <span class="arrival-time">${minutesUntil} min</span>
            </div>
        `;
    }
}
```

#### **3. Electron Main Process**
```javascript
const { app, BrowserWindow } = require('electron');
const path = require('path');

function createWindow() {
    const mainWindow = new BrowserWindow({
        width: 1920,
        height: 1080,
        fullscreen: true, // Kiosk mode
        webPreferences: {
            nodeIntegration: true,
            contextIsolation: false
        },
        autoHideMenuBar: true,
        kiosk: process.env.KIOSK_MODE === 'true'
    });
    
    mainWindow.loadFile('index.html');
    
    // Hide cursor for kiosk deployment
    if (process.env.KIOSK_MODE === 'true') {
        mainWindow.webContents.insertCSS('* { cursor: none !important; }');
    }
}

app.whenReady().then(createWindow);
```

## Key Features

### 🎯 **Real-Time Accuracy**
- **Live arrival predictions** from OneBusAway's proven API
- **Automatic updates** every 30 seconds for current information
- **Prediction accuracy** leveraging GPS and schedule data
- **Multi-modal support** for buses, light rail, and ferries

### 🖥️ **Display Optimization**
- **Responsive design** adapting to various screen sizes
- **High contrast mode** for outdoor and bright environments
- **Large typography** ensuring readability from distance
- **Status indicators** showing service alerts and delays

### 🔧 **Deployment Flexibility**
- **Configurable stop IDs** for any OneBusAway-supported region
- **Multiple display modes** (portrait, landscape, split-screen)
- **Energy-efficient operation** suitable for battery-powered displays
- **Remote configuration** through environment variables

### 🛡️ **Reliability Features**
- **Graceful error handling** with informative error messages
- **Offline mode** displaying last known schedule information
- **Network resilience** with automatic reconnection attempts
- **Watchdog functionality** preventing display freezing

## Deployment Scenarios

### **Public Transit Stops**
Perfect for community installations:
- **Bus stop displays** showing arrival times for all routes
- **Community centers** with transit information for visitors
- **University campuses** with student-accessible transit data
- **Senior centers** providing large-text transit information

### **Raspberry Pi Implementation**
Optimized for small computer deployment:

```bash
# Installation script for Raspberry Pi
#!/bin/bash
# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

# Clone and setup application
git clone https://github.com/KyleODore/OneBusAwayStop-Electron.git
cd OneBusAwayStop-Electron
npm install

# Configure for kiosk mode
export KIOSK_MODE=true
export STOP_ID=your_stop_id_here
export API_KEY=your_api_key_here

# Start application
npm start
```

### **Hardware Requirements**
- **Minimum**: Raspberry Pi 3B+ with 1GB RAM
- **Recommended**: Raspberry Pi 4 with 4GB RAM
- **Display**: Any HDMI-compatible monitor or TV
- **Network**: WiFi or Ethernet connection
- **Power**: 5V USB-C power supply

## Technical Challenges & Solutions

### **1. Resource Optimization**
**Challenge**: Running smoothly on limited Raspberry Pi resources
**Solution**: 
- Optimized DOM updates using virtual scrolling
- Efficient memory management with object pooling
- CSS animations instead of JavaScript for smooth transitions
- Lazy loading of non-critical components

### **2. Network Reliability**
**Challenge**: Handling intermittent internet connectivity
**Solution**:
- Exponential backoff for API retry logic
- Local caching of schedule data
- Graceful degradation to cached information
- Clear status indicators for connection state

### **3. Display Longevity**
**Challenge**: 24/7 operation without display burn-in
**Solution**:
- Screen saver mode during low-activity periods
- Automatic brightness adjustment based on time
- Content rotation to prevent static element burn-in
- Power management for energy efficiency

## Community Impact

### **Accessibility Improvement**
The project addresses real community needs:
- **Digital equity**: Low-cost solution for underserved communities
- **Accessibility**: Large text and high contrast for visual accessibility
- **Language support**: Configurable for multilingual communities
- **Universal design**: Simple interface usable by all age groups

### **Open Source Benefits**
- **Community contributions**: Open for local customization
- **Cost effectiveness**: Free alternative to commercial solutions
- **Educational value**: Learning resource for students and makers
- **Sustainability**: Long-term viability without vendor lock-in

## Skills Demonstrated

### **Technical Competencies**
- **Cross-Platform Development**: Electron framework mastery
- **API Integration**: RESTful service consumption and error handling
- **Embedded Systems**: Raspberry Pi optimization and deployment
- **Frontend Development**: Responsive, accessible user interface design
- **System Administration**: Linux deployment and configuration

### **Problem-Solving Approach**
- **Community-Focused Design**: Understanding real-world deployment challenges
- **Resource Constraints**: Optimizing for limited hardware capabilities
- **Reliability Engineering**: Building systems for 24/7 operation
- **User Experience**: Creating interfaces for public, diverse usage

## Future Enhancements

### **Technical Improvements**
- **Multi-stop support**: Displaying multiple nearby stops
- **Arrival history**: Historical data for route analysis
- **Voice announcements**: Audio accessibility features
- **Mobile app**: Companion app for personal use

### **Community Features**
- **Service alerts**: Real-time disruption notifications
- **Route planning**: Next connection suggestions
- **Accessibility**: Enhanced support for visual/hearing impairments
- **Multilingual**: Support for multiple languages

## Connection to Professional Work

This project reflects skills directly applicable to enterprise development:

### **At SAP SuccessFactors**
- **API Integration**: Experience valuable for enterprise service connections
- **Cross-Platform Deployment**: Relevant for global software distribution
- **Resource Optimization**: Critical for high-performance enterprise applications
- **User Experience**: Important for accessible enterprise software

### **System Design Principles**
- **Reliability**: 24/7 operation requirements similar to enterprise systems
- **Scalability**: Deployment across multiple locations mirrors enterprise distribution
- **Maintainability**: Open source contribution standards apply to all development
- **Documentation**: Clear documentation essential for team collaboration

---

Durn Bus Away represents the intersection of **practical problem-solving**, **community service**, and **technical innovation**. By creating an accessible, deployable solution for transit information, this project demonstrates how technology can address real community needs while showcasing technical skills in cross-platform development, API integration, and embedded systems deployment.

The project embodies my commitment to using technology for social good, creating solutions that are not only technically sound but also serve meaningful purposes in improving daily life for community members.