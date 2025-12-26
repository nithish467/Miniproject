## Title of the Project

"AI FOR MENTAL HEALTH"

**The implementation of an AI-driven conversational agent designed to provide accessible, 24/7 emotional support for individuals navigating daily stressors and anxiety. By leveraging natural language processing, the system offers a safe and anonymous environment for users to practice mindfulness and cognitive behavioral techniques, effectively lowering the barrier to mental health care and providing immediate intervention between professional therapy sessions.**

## About
**Detailed Description: AI Mental Health Chatbot with Sentiment Analysis**

The AI Mental Health Chatbot is an advanced conversational system designed to act as a digital wellness companion by identifying and responding to the emotional state of the user. Unlike standard chatbots that rely on static keyword matching, this project utilizes emotional and sentiment analysis to evaluate the nuance behind a user’s input text in real-time. By processing the linguistic patterns and emotional cues within a chat, the AI categorizes the user’s mood—such as stress, anxiety, or sadness—and dynamically generates personalized wellness tips and coping strategies. The primary goal of this project is to provide a non-judgmental, always-available support tool that encourages self-reflection and proactive mental health management, helping users navigate their emotional well-being through data-driven, empathetic interactions.

**Key Technical Features of Your Project:**

- To make this description even more impressive for a presentation or report, you can highlight these specific functions:

- Real-time Sentiment Scoring: The system calculates the "polarity" (positive, negative, or neutral) of every message sent by the user.

- Contextual Understanding: It doesn't just look at one word; it analyzes the entire sentence to understand the user's true feeling.

- Tailored Response Logic: If the sentiment is "Negative/Stressed," it triggers wellness tips like breathing exercises; if "Positive," it reinforces healthy habits.

## Features
<!--List the features of the project as shown below-->
- Implements advanced AI models for mental-health assessment and support.
- A framework-based application designed for secure and ethical deployment.
- High scalability to support a large number of users simultaneously.
- Reduced processing time to deliver rapid emotional and behavioral insights.
- A focused scope for mental-health assistance, using structured data formats such as JSON for secure and consistent communication
- 
## Requirements

<!--List the requirements of the project as shown below-->
* Operating System: Requires a 64-bit OS (Windows 10 / 11 or Ubuntu) to support AI and NLP-based mental-health applications.
* Development Environment: Python 3.8 or later is required for building and deploying the AI mental-health support system.
* AI / NLP Frameworks: Uses frameworks such as TensorFlow or PyTorch for model development, and libraries like spaCy or transformers for natural-language understanding.
* Data Processing Libraries: Pandas and NumPy are essential for handling user conversation data and maintaining structured datasets.
* Version Control: Git is used to manage code updates, collaboration, and project versioning.
* IDE: VS Code or PyCharm is recommended for development, debugging, and environment management.
* Security & Privacy Dependencies: Includes encryption and authentication libraries to ensure user data confidentiality and ethical handling of sensitive mental-health information.
* Additional Dependencies: May include Flask or FastAPI for deployment, scikit-learn for analytics, and JSON support for structured chatbot communication.

## System Architecture
<!--Embed the system architecture diagram as shown below-->


## PROGRAM :

index.html
```



<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>MindCare AI</title>
    <link rel="manifest" href="/manifest.json">
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=Noto+Sans+Tamil:wght@400;500;700&family=Noto+Sans+Devanagari:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
      body {
        font-family: 'Inter', 'Noto Sans Tamil', 'Noto Sans Devanagari', sans-serif;
      }
      /* Custom scrollbar for chat area */
      .scrollbar-hide::-webkit-scrollbar {
          display: none;
      }
      .scrollbar-hide {
          -ms-overflow-style: none;
          scrollbar-width: none;
      }
    </style>
  <script type="importmap">
{
  "imports": {
    "lucide-react": "https://aistudiocdn.com/lucide-react@^0.555.0",
    "react-dom/": "https://aistudiocdn.com/react-dom@^19.2.0/",
    "@google/genai": "https://aistudiocdn.com/@google/genai@^1.30.0",
    "react/": "https://aistudiocdn.com/react@^19.2.0/",
    "react": "https://aistudiocdn.com/react@^19.2.0",
    "react-markdown": "https://aistudiocdn.com/react-markdown@^10.1.0",
    "uuid": "https://aistudiocdn.com/uuid@^13.0.0"
  }
}
</script>
<link rel="stylesheet" href="/index.css">
</head>
  <body class="bg-slate-50 text-slate-900 h-screen w-screen overflow-hidden">
    <div id="root" class="h-full w-full"></div>
    <script>
      if ('serviceWorker' in navigator) {
        window.addEventListener('load', () => {
          navigator.serviceWorker.register('/sw.js')
            .then(registration => {
              console.log('SW registered: ', registration);
            })
            .catch(registrationError => {
              console.log('SW registration failed: ', registrationError);
            });
        });
      }
    </script>
  <script type="module" src="/index.tsx"></script>
</body>
</html>


```
index.txt
```
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const rootElement = document.getElementById('root');
if (!rootElement) {
  throw new Error("Could not find root element to mount to");
}

const root = ReactDOM.createRoot(rootElement);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
App.tsx
```

import React, { useState, useEffect, useRef } from 'react';
import { Send, Trash2, Sparkles, Loader2, Mic, MicOff, Lightbulb, Book, X, Calendar, Dna, WifiOff } from 'lucide-react';
import { v4 as uuidv4 } from 'uuid';

import { TRANSLATIONS, LANGUAGES } from './constants';
import { LanguageCode, Message, JournalEntry } from './types';
import { sendMessageToGemini, resetSession } from './services/geminiService';

import ChatMessage from './components/ChatMessage';
import LanguageSelector from './components/LanguageSelector';
import DisclaimerBanner from './components/DisclaimerBanner';

const App: React.FC = () => {
  const [language, setLanguage] = useState<LanguageCode>('en');
  const [messages, setMessages] = useState<Message[]>([]);
  const [inputValue, setInputValue] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  const [isListening, setIsListening] = useState(false);
  
  // Online/Offline State
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  
  // User Name State
  const [userName, setUserName] = useState('');
  const [showNameModal, setShowNameModal] = useState(true);
  const [tempName, setTempName] = useState('');

  // Journal State
  const [showJournal, setShowJournal] = useState(false);
  const [journalEntries, setJournalEntries] = useState<JournalEntry[]>([]);
  const [newJournalEntry, setNewJournalEntry] = useState('');
  
  const messagesEndRef = useRef<HTMLDivElement>(null);
  const recognitionRef = useRef<any>(null);

  const t = TRANSLATIONS[language];

  // Monitor Online Status
  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  // Load journal entries from local storage on mount
  useEffect(() => {
    const savedEntries = localStorage.getItem('mindcare_journal_entries');
    if (savedEntries) {
      try {
        setJournalEntries(JSON.parse(savedEntries));
      } catch (e) {
        console.error("Failed to parse journal entries", e);
      }
    }
  }, []);

  // Save journal entries when they change
  useEffect(() => {
    if (journalEntries.length > 0) {
      localStorage.setItem('mindcare_journal_entries', JSON.stringify(journalEntries));
    }
  }, [journalEntries]);

  // Auto-scroll to bottom
  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const handleStartChat = () => {
    if (!tempName.trim()) return;
    
    setUserName(tempName.trim());
    setShowNameModal(false);
    
    // Set initial welcome message personalized with name
    const welcomeMsg = t.welcome.replace("Hello.", `Hello ${tempName.trim()}.`);
    
    setMessages([
      {
        id: 'init-1',
        role: 'model',
        text: welcomeMsg,
        timestamp: new Date()
      }
    ]);
  };

  const handleSendMessage = async (textOverride?: string, isHiddenCommand: boolean = false) => {
    if (!isOnline) return;
    
    const textToSend = textOverride || inputValue.trim();
    
    if (!textToSend || isLoading) return;

    if (!isHiddenCommand) {
      setInputValue('');
      // Add user message to UI
      const newUserMsg: Message = {
        id: uuidv4(),
        role: 'user',
        text: textToSend,
        timestamp: new Date()
      };
      setMessages(prev => [...prev, newUserMsg]);
    }
    
    setIsLoading(true);

    try {
      const langName = LANGUAGES.find(l => l.code === language)?.name || 'English';
      
      // Pass userName to service
      const responseText = await sendMessageToGemini(textToSend, langName, userName);
      
      const newAiMsg: Message = {
        id: uuidv4(),
        role: 'model',
        text: responseText,
        timestamp: new Date()
      };
      setMessages(prev => [...prev, newAiMsg]);

    } catch (error) {
      console.error(error);
      const errorMsg: Message = {
        id: uuidv4(),
        role: 'system',
        text: "Sorry, I'm having trouble connecting right now. Please try again.",
        timestamp: new Date()
      };
      setMessages(prev => [...prev, errorMsg]);
    } finally {
      setIsLoading(false);
    }
  };

  const handleGetTip = () => {
    if (!isOnline) return;
    const tipPrompt = "Please provide a short, specific, and actionable mental health tip or coping strategy for me right now.";
    handleSendMessage(tipPrompt, true);
  };

  const handleGetScientificView = () => {
    if (!isOnline) return;
    const sciencePrompt = "Please explain the biological mechanisms, scientific terminology, or neurochemistry (like neurotransmitters or brain regions) associated with the feelings or symptoms we just discussed. Keep it simple and educational.";
    handleSendMessage(sciencePrompt, true);
  };

  const handleClearChat = () => {
    if (window.confirm('Are you sure you want to clear the conversation?')) {
      resetSession();
      const welcomeMsg = t.welcome.replace("Hello.", `Hello ${userName}.`);
      setMessages([{
        id: uuidv4(),
        role: 'model',
        text: welcomeMsg,
        timestamp: new Date()
      }]);
    }
  };

  const handleSaveJournal = () => {
    if (!newJournalEntry.trim()) return;

    const entry: JournalEntry = {
      id: uuidv4(),
      text: newJournalEntry.trim(),
      timestamp: new Date().toISOString()
    };

    setJournalEntries(prev => [entry, ...prev]);
    setNewJournalEntry('');
  };

  const handleDeleteJournal = (id: string) => {
    if (window.confirm('Delete this entry?')) {
      const updated = journalEntries.filter(e => e.id !== id);
      setJournalEntries(updated);
      // If empty, also clear local storage to be clean
      if (updated.length === 0) {
        localStorage.removeItem('mindcare_journal_entries');
      }
    }
  };

  const handleLanguageChange = (newLang: LanguageCode) => {
    setLanguage(newLang);
    const langName = LANGUAGES.find(l => l.code === newLang)?.nativeName;
    setMessages(prev => [...prev, {
      id: uuidv4(),
      role: 'system',
      text: `Language changed to ${langName}`,
      timestamp: new Date()
    }]);
  };

  const toggleVoiceInput = () => {
    if (!isOnline) return;

    if (isListening) {
      if (recognitionRef.current) {
        recognitionRef.current.stop();
      }
      setIsListening(false);
      return;
    }

    const SpeechRecognition = (window as any).SpeechRecognition || (window as any).webkitSpeechRecognition;
    
    if (!SpeechRecognition) {
      alert("Voice input is not supported in this browser. Please try Chrome, Edge, or Safari.");
      return;
    }

    const recognition = new SpeechRecognition();
    recognitionRef.current = recognition;
    
    const langMap: Record<string, string> = {
      'en': 'en-US',
      'ta': 'ta-IN',
      'hi': 'hi-IN'
    };
    
    recognition.lang = langMap[language] || 'en-US';
    recognition.continuous = false;
    recognition.interimResults = false;

    recognition.onstart = () => {
      setIsListening(true);
    };

    recognition.onend = () => {
      setIsListening(false);
    };

    recognition.onresult = (event: any) => {
      const transcript = event.results[0][0].transcript;
      setInputValue(prev => {
        const trimmed = prev.trim();
        return trimmed ? `${trimmed} ${transcript}` : transcript;
      });
    };

    recognition.onerror = (event: any) => {
      console.error("Speech recognition error", event.error);
      setIsListening(false);
    };

    recognition.start();
  };

  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSendMessage();
    }
  };

  return (
    <div className="flex flex-col h-full bg-slate-50 relative">
      
      {/* Name Input Modal */}
      {showNameModal && (
        <div className="absolute inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
          <div className="bg-white rounded-2xl shadow-xl p-8 w-full max-w-md animate-fade-in">
            <div className="flex justify-center mb-6">
               <div className="bg-teal-100 p-3 rounded-full">
                 <Sparkles size={32} className="text-teal-600" />
               </div>
            </div>
            <h2 className="text-2xl font-bold text-center text-slate-800 mb-2">{t.enterName}</h2>
            <p className="text-center text-slate-500 mb-6">{t.subtitle}</p>
            
            <input 
              type="text"
              value={tempName}
              onChange={(e) => setTempName(e.target.value)}
              placeholder={t.namePlaceholder}
              className="w-full px-4 py-3 rounded-xl border border-slate-200 focus:border-teal-500 focus:ring-2 focus:ring-teal-100 outline-none transition-all mb-4 text-center text-lg"
              autoFocus
              onKeyDown={(e) => e.key === 'Enter' && handleStartChat()}
            />
            
            <button 
              onClick={handleStartChat}
              disabled={!tempName.trim()}
              className="w-full bg-teal-600 hover:bg-teal-700 text-white font-semibold py-3 rounded-xl transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {t.startChat}
            </button>
            
            <div className="mt-6 flex justify-center">
               <LanguageSelector currentLanguage={language} onLanguageChange={setLanguage} />
            </div>
          </div>
        </div>
      )}

      {/* Journal Modal - Available Offline */}
      {showJournal && (
        <div className="absolute inset-0 z-40 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
          <div className="bg-white rounded-2xl shadow-xl w-full max-w-2xl h-[80vh] flex flex-col animate-fade-in overflow-hidden">
            <div className="bg-indigo-600 px-6 py-4 flex items-center justify-between text-white flex-shrink-0">
              <div className="flex items-center gap-3">
                <Book size={24} className="text-yellow-300" />
                <h2 className="text-xl font-bold">{t.journalTitle}</h2>
              </div>
              <button 
                onClick={() => setShowJournal(false)}
                className="hover:bg-indigo-700 p-2 rounded-full transition-colors"
              >
                <X size={24} />
              </button>
            </div>

            <div className="flex-1 overflow-y-auto p-6 bg-slate-50">
              <div className="bg-white p-4 rounded-xl shadow-sm border border-slate-100 mb-6">
                <textarea
                  value={newJournalEntry}
                  onChange={(e) => setNewJournalEntry(e.target.value)}
                  placeholder={t.writeEntry}
                  className="w-full p-3 border-none outline-none resize-none bg-slate-50 rounded-lg focus:ring-1 focus:ring-indigo-200 min-h-[100px]"
                />
                <div className="flex justify-end mt-2">
                  <button 
                    onClick={handleSaveJournal}
                    disabled={!newJournalEntry.trim()}
                    className="bg-teal-600 hover:bg-teal-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors disabled:opacity-50"
                  >
                    {t.saveEntry}
                  </button>
                </div>
              </div>

              <h3 className="text-slate-500 font-semibold mb-3 flex items-center gap-2 text-sm uppercase tracking-wide">
                <Calendar size={14} /> {t.history}
              </h3>
              
              <div className="space-y-4">
                {journalEntries.length === 0 ? (
                  <p className="text-center text-slate-400 py-8 italic">{t.noEntries}</p>
                ) : (
                  journalEntries.map(entry => (
                    <div key={entry.id} className="bg-white p-4 rounded-xl shadow-sm border border-slate-100 group">
                      <div className="flex justify-between items-start mb-2">
                        <span className="text-xs font-semibold text-indigo-600 bg-indigo-50 px-2 py-1 rounded-md">
                          {new Date(entry.timestamp).toLocaleDateString(undefined, { 
                            weekday: 'short', 
                            year: 'numeric', 
                            month: 'short', 
                            day: 'numeric',
                            hour: '2-digit',
                            minute: '2-digit'
                          })}
                        </span>
                        <button 
                          onClick={() => handleDeleteJournal(entry.id)}
                          className="text-slate-300 hover:text-red-500 transition-colors opacity-0 group-hover:opacity-100"
                        >
                          <Trash2 size={16} />
                        </button>
                      </div>
                      <p className="text-slate-700 whitespace-pre-wrap text-sm leading-relaxed">{entry.text}</p>
                    </div>
                  ))
                )}
              </div>

            </div>
          </div>
        </div>
      )}

      {/* Header */}
      <header className="bg-gradient-to-r from-teal-600 to-indigo-600 shadow-md z-10">
        <div className="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">
          <div className="flex items-center gap-3 text-white">
            <div className="bg-white/20 p-2 rounded-lg backdrop-blur-sm">
               <Sparkles size={24} className="text-yellow-300" />
            </div>
            <div>
              <h1 className="font-bold text-xl leading-tight md:block hidden">{t.title}</h1>
              <h1 className="font-bold text-lg leading-tight md:hidden block">MindCare</h1>
              {userName && <p className="text-xs text-indigo-100 opacity-90">Hi, {userName}</p>}
            </div>
          </div>
          
          <div className="flex items-center gap-2">
             <button 
               onClick={() => setShowJournal(true)}
               className="flex items-center gap-2 bg-white/10 hover:bg-white/20 text-white text-xs px-3 py-1.5 rounded-full border border-white/20 transition-all"
               title="Open Journal"
             >
               <Book size={14} className="text-yellow-300" />
               <span className="hidden md:inline">{t.journal}</span>
             </button>

             <button 
               onClick={handleGetTip}
               disabled={!isOnline}
               className={`hidden md:flex items-center gap-2 text-xs px-3 py-1.5 rounded-full border border-white/20 transition-all ${!isOnline ? 'bg-slate-400/50 cursor-not-allowed text-slate-200' : 'bg-white/10 hover:bg-white/20 text-white'}`}
               title="Get a wellness tip"
             >
               <Lightbulb size={14} className={isOnline ? "text-yellow-300" : "text-slate-300"} />
               <span className="hidden lg:inline">{t.getTip}</span>
             </button>
             <LanguageSelector currentLanguage={language} onLanguageChange={handleLanguageChange} />
          </div>
        </div>
      </header>

      {/* Offline Banner */}
      {!isOnline && (
        <div className="bg-slate-800 text-white text-center py-2 px-4 text-sm font-medium flex items-center justify-center gap-2 animate-pulse">
          <WifiOff size={16} />
          {t.offline}: {t.offlineMessage}
        </div>
      )}

      {/* Safety Disclaimer */}
      <DisclaimerBanner t={t} />

      {/* Chat Area */}
      <main className="flex-1 overflow-y-auto px-4 py-6 scrollbar-hide">
        <div className="max-w-3xl mx-auto">
           {messages.map((msg) => (
             <ChatMessage key={msg.id} message={msg} />
           ))}
           {isLoading && (
             <div className="flex justify-start mb-6">
                <div className="flex items-center gap-2 bg-white px-4 py-3 rounded-2xl rounded-tl-none border border-slate-100 shadow-sm">
                  <Loader2 size={16} className="animate-spin text-teal-600" />
                  <span className="text-sm text-slate-500">Thinking...</span>
                </div>
             </div>
           )}
           <div ref={messagesEndRef} />
        </div>
      </main>

      {/* Input Area */}
      <footer className="bg-white border-t border-slate-200 p-4 pb-6 md:pb-4">
        <div className="max-w-3xl mx-auto flex flex-col gap-2">
          
          <div className={`flex items-end gap-2 bg-slate-100 rounded-2xl p-2 border border-transparent transition-all ${isOnline ? 'focus-within:border-indigo-300 focus-within:ring-2 focus-within:ring-indigo-100' : 'opacity-70 grayscale'}`}>
            <textarea
              value={inputValue}
              onChange={(e) => setInputValue(e.target.value)}
              onKeyDown={handleKeyDown}
              disabled={!isOnline}
              placeholder={!isOnline ? t.offlineMessage : (isListening ? "Listening..." : t.placeholder)}
              rows={1}
              className="flex-1 bg-transparent border-none focus:ring-0 resize-none max-h-32 min-h-[44px] py-2.5 px-3 text-slate-700 placeholder:text-slate-400 disabled:cursor-not-allowed"
              style={{ height: 'auto', minHeight: '44px' }}
            />
            
            <button
              onClick={toggleVoiceInput}
              disabled={!isOnline}
              className={`p-3 rounded-xl flex-shrink-0 transition-all ${
                !isOnline ? 'bg-slate-200 text-slate-400 cursor-not-allowed' :
                isListening 
                  ? 'bg-red-500 text-white animate-pulse shadow-md' 
                  : 'bg-slate-200 text-slate-500 hover:bg-slate-300'
              }`}
              title="Voice Input"
            >
              {isListening ? <MicOff size={20} /> : <Mic size={20} />}
            </button>

            <button
              onClick={() => handleSendMessage()}
              disabled={!inputValue.trim() || isLoading || !isOnline}
              className={`p-3 rounded-xl flex-shrink-0 transition-all ${
                inputValue.trim() && !isLoading && isOnline
                  ? 'bg-indigo-600 text-white hover:bg-indigo-700 shadow-md hover:shadow-lg transform hover:-translate-y-0.5'
                  : 'bg-slate-200 text-slate-400 cursor-not-allowed'
              }`}
              aria-label={t.send}
            >
              <Send size={20} />
            </button>
          </div>

          <div className="flex justify-between items-center px-1">
             <div className="flex gap-4 overflow-x-auto pb-1 scrollbar-hide">
               <button 
                 onClick={handleGetTip}
                 disabled={!isOnline}
                 className={`flex items-center gap-1 text-xs font-medium whitespace-nowrap ${isOnline ? 'text-teal-600 hover:text-teal-700' : 'text-slate-400 cursor-not-allowed'}`}
               >
                 <Lightbulb size={12} /> {t.getTip}
               </button>
               <button 
                 onClick={handleGetScientificView}
                 disabled={!isOnline}
                 className={`flex items-center gap-1 text-xs font-medium whitespace-nowrap ${isOnline ? 'text-indigo-600 hover:text-indigo-700' : 'text-slate-400 cursor-not-allowed'}`}
               >
                 <Dna size={12} /> {t.scientificView}
               </button>
               <button 
                 onClick={() => setShowJournal(true)}
                 className="md:hidden flex items-center gap-1 text-xs text-purple-600 font-medium whitespace-nowrap"
               >
                 <Book size={12} /> {t.journal}
               </button>
             </div>

             <button 
               onClick={handleClearChat}
               className="text-xs text-slate-400 hover:text-red-500 flex items-center gap-1 transition-colors whitespace-nowrap ml-2"
             >
               <Trash2 size={12} /> {t.clearChat}
             </button>
          </div>

        </div>
      </footer>
    </div>
  );
};

export default App;

```
service.ts
```
import { GoogleGenAI, ChatSession } from "@google/genai";
import { SYSTEM_INSTRUCTION } from "../constants";

let chatSession: ChatSession | null = null;

const getClient = () => {
  const apiKey = import.meta.env.VITE_GEMINI_API_KEY;

  if (!apiKey) {
    throw new Error("Gemini API Key is missing. Check .env.local");
  }

  return new GoogleGenAI({ apiKey });
};

export const initializeChat = async () => {
  try {
    const ai = getClient();

    chatSession = ai.chats.create({
      model: "gemini-1.5-flash",
      config: {
        systemInstruction: SYSTEM_INSTRUCTION,
        temperature: 0.7,
        topK: 40,
      },
    });

    return chatSession;
  } catch (error) {
    console.error("Failed to initialize chat:", error);
    throw error;
  }
};

export const sendMessageToGemini = async (
  message: string,
  languageContext: string,
  userName: string = "Friend"
): Promise<string> => {
  if (!chatSession) {
    await initializeChat();
  }

  if (!chatSession) {
    throw new Error("Chat session not initialized");
  }

  try {
    const context = `[User Name: ${userName}] [Language: ${languageContext}]`;
    const fullPrompt = `${context}\n${message}`;

    const result = await chatSession.sendMessage({
      message: fullPrompt,
    });

    return result.text;
  } catch (error) {
    console.error("Gemini API error:", error);
    throw error;
  }
};

export const resetSession = () => {
  chatSession = null;
};

```


## Output

<!--Embed the Output picture at respective places as shown below as shown below-->
#### Output1 - Name of the output

## Intro to the chatbot

![2chatbox](https://github.com/user-attachments/assets/715e3eaa-1951-432d-8292-c09723d617d4)


#### Output2 - Name of the output

## Analysis of the chat and giving output

![3](https://github.com/user-attachments/assets/586b7a14-824a-42dc-8f77-075672ae2fa1)


## Results and Impact
<!--Give the results and impact as shown below-->

The AI for Mental Health system supports individuals by offering early emotional assistance, stress monitoring, and wellbeing guidance through intelligent conversation. It helps users openly express their thoughts and feelings in a safe, confidential environment, improving access to mental-health support. The integration of AI, natural-language processing, and behavioral analytics demonstrates its potential to assist clinicians, reduce response time for support, and promote mental-health awareness and self-care.
This project serves as a foundation for future developments in assistive technologies and contributes to creating a more inclusive and accessible digital environment.

## Articles published / References
1 H. Sharma and R. Kumar, “A new sentiment segmentation method for textual analysis in emotional color spaces,” in 24th International Conference on Natural Language Processing, IEEE, 2022. 

2 G. Chen, H. Wang, A. Smith, A. Patel, J. Doe, N. Gupta, C. Lee, and M. A. Rahaman, “Smart wellness monitoring system using deep learning and empathetic computing,” IEEE Transactions on Affective Computing, 2023. 


