# zig

import tkinter as tk
from tkinter import ttk, messagebox
import json
import os

class EnglishInputMethod:
    """
    A simple English input method with word prediction and auto-completion
    """
    
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("Simple English Input Method")
        self.root.geometry("600x400")
        self.root.configure(bg='#f0f8ff')
        
        # Word dictionary for predictions
        self.word_dict = self.load_word_dictionary()
        
        # User input history for personalized predictions
        self.user_history = self.load_user_history()
        
        # Initialize GUI components test-ind-api+fyinformation+cc food
        self.setup_gui()
        
    def load_word_dictionary(self):
        """
        Load the English word dictionary with common words and their frequencies
        """
        base_dict = {
            "hello": 100, "world": 90, "python": 85, "program": 80,
            "code": 75, "input": 70, "method": 65, "simple": 60,
            "english": 55, "dictionary": 50, "predict": 45,
            "completion": 40, "typing": 35, "suggestion": 30,
            "keyboard": 25, "language": 20, "computer": 15
        }
        
        # Try to load custom dictionary from file
        try:
            if os.path.exists("word_dict.json"):
                with open("word_dict.json", "r", encoding='utf-8') as f:
                    custom_dict = json.load(f)
                    base_dict.update(custom_dict)
        except Exception as e:
            print(f"Error loading custom dictionary: {e}")
            
        return base_dict
    
    def load_user_history(self):
        """
        Load user typing history for personalized predictions
        """
        try:
            if os.path.exists("user_history.json"):
                with open("user_history.json", "r", encoding='utf-8') as f:
                    return json.load(f)
        except:
            return {}
    
    def save_user_history(self):
        """
        Save user typing history to file
        """
        try:
            with open("user_history.json", "w", encoding='utf-8') as f:
                json.dump(self.user_history, f, ensure_ascii=False, indent=2)
        except Exception as e:
            print(f"Error saving user history: {e}")
    
    def get_word_predictions(self, current_text):
        """
        Get word predictions based on current input text
        """
        if not current_text:
            return []
            
        current_text = current_text.lower()
        predictions = []
        
        # Check for exact matches and partial matches
        for word, frequency in self.word_dict.items():
            if word.startswith(current_text):
                # Calculate score based on frequency and user history
                score = frequency
                if word in self.user_history:
                    score += self.user_history[word] * 10
                predictions.append((word, score))
        
        # Sort by score (highest first)
        predictions.sort(key=lambda x: x[1], reverse=True)
        
        return [word for word, score in predictions[:5]]
    
    def setup_gui(self):
        """
        Setup the graphical user interface components
        """
        # Main frame
        main_frame = ttk.Frame(self.root, padding="10")
        main_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))
        
        # Title label
        title_label = ttk.Label(main_frame, 
                               text="Simple English Input Method",
                               font=("Arial", 16, "bold"),
                               foreground="#2c3e50")
        title_label.grid(row=0, column=0, columnspan=2, pady=(0, 20))
        
        # Input label
        input_label = ttk.Label(main_frame, 
                               text="Type your text:",
                               font=("Arial", 11))
        input_label.grid(row=1, column=0, sticky=tk.W, pady=(0, 5))
        
        # Text input area
        self.text_input = tk.Text(main_frame, 
                                 height=8, 
                                 width=60,
                                 font=("Arial", 12),
                                 wrap=tk.WORD)
        self.text_input.grid(row=2, column=0, columnspan=2, pady=(0, 10))
        self.text_input.bind('<KeyRelease>', self.on_key_release)
        
        # Prediction label 
        pred_label = ttk.Label(main_frame, 
                              text="Word predictions:",
                              font=("Arial", 11))
        pred_label.grid(row=3, column=0, sticky=tk.W, pady=(0, 5))
        
        # Prediction listbox
        self.prediction_listbox = tk.Listbox(main_frame,
                                           height=5,
                                           font=("Arial", 11))
        self.prediction_listbox.grid(row=4, column=0, columnspan=2, pady=(0, 10))
        self.prediction_listbox.bind('<Double-Button-1>', self.on_prediction_select)
        
        # Buttons frame
        button_frame = ttk.Frame(main_frame)
        button_frame.grid(row=5, column=0, columnspan=2, pady=10)
        
        # Clear button
        clear_btn = ttk.Button(button_frame,
                             text="Clear Text",
                             command=self.clear_text)
        clear_btn.grid(row=0, column=0, padx=(0, 10))
        
        # Add word button
        add_word_btn = ttk.Button(button_frame,
                                text="Add Word to Dictionary",
                                command=self.add_word_to_dict)
        add_word_btn.grid(row=0, column=1, padx=(0, 10))
        
 
