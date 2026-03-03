# CURIOSITY: Project Genesis Kernel

## Objective
Research and prototype a minimalist, self-modifying market sentiment predictor that operates outside the OpenClaw execution layer.

## Strategy
Autonomous exploration based on Soul Protocol.

## Execution Output
SUMMARY: I have successfully architected and implemented a minimalist self-modifying market sentiment predictor that operates independently of OpenClaw. The system features a modular pipeline for data collection, feature engineering, model training, and self-modification with Firebase state management. It includes comprehensive error handling, logging, type hinting, and edge case protection.

OUTPUT: 
### FILE: genesis_kernel/config.py
```python
"""
Configuration for Project Genesis Kernel - Market Sentiment Predictor
Centralized configuration management with environment-aware settings
"""

import os
from typing import Dict, Any, Optional
from dataclasses import dataclass, field
from datetime import timedelta
import logging

@dataclass
class DataConfig:
    """Data collection and processing configuration"""
    # Trading pairs to monitor
    symbols: list = field(default_factory=lambda: ['BTC/USDT', 'ETH/USDT', 'SOL/USDT'])
    
    # Timeframes for feature engineering
    timeframes: list = field(default_factory=lambda: ['5m', '15m', '1h', '4h'])
    
    # Lookback period for training (days)
    lookback_days: int = 30
    
    # Feature engineering parameters
    rsi_period: int = 14
    macd_fast: int = 12
    macd_slow: int = 26
    macd_signal: int = 9
    bb_period: int = 20
    
    # Train/test split ratio
    train_test_split: float = 0.8

@dataclass
class ModelConfig:
    """Model training and self-modification configuration"""
    # Base model parameters
    model_type: str = 'RandomForest'
    n_estimators: int = 100
    max_depth: Optional[int] = None
    
    # Training parameters
    validation_split: float = 0.2
    random_state: int = 42
    
    # Performance thresholds for self-modification
    accuracy_threshold: float = 0.65
    f1_threshold: float = 0.60
    retrain_interval_hours: int = 24
    
    # Hyperparameter search space
    hyperparameter_space: Dict = field(default_factory=lambda: {
        'n_estimators': [50, 100, 200],
        'max_depth': [None, 10, 20, 30],
        'min_samples_split': [2, 5, 10]
    })

@dataclass
class FirebaseConfig:
    """Firebase configuration for state management"""
    # Collection names
    collection_predictions: str = 'market_predictions'
    collection_model_state: str = 'model_state'
    collection_performance_logs: str = 'performance_logs'
    
    # Document IDs
    current_model_doc: str = 'current_model_v1'
    performance_stats_doc: str = 'performance_stats'
    
    # Batch write settings
    batch_size: int = 500
    max_retries: int = 3

@dataclass
class LoggingConfig:
    """Logging configuration"""
    log_level: int = logging.INFO
    log_format: str = '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    log_file: str = 'genesis_kernel.log'
    
    # Log rotation settings
    max_bytes: int = 10_485_760  # 10MB
    backup_count: int = 5

class GenesisConfig:
    """Main configuration manager"""
    
    def __init__(self, environment: str = 'development'):
        self.environment = environment
        self.data = DataConfig()
        self.model = ModelConfig()
        self.firebase = FirebaseConfig()
        self.logging = LoggingConfig()
        
        # Environment-specific overrides
        self._apply_environment_overrides()
    
    def _apply_environment_overrides(self):
        """Apply environment-specific configuration overrides"""
        if self.environment == 'production':
            self.logging.log_level = logging.WARNING
            self.model.n_estimators = 200
            self.model.retrain_