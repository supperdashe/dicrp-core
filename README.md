# dicrp-core
A deterministic, state-machine-driven gatekeeper to eliminate LLM hallucinations and out-of-control agent loops by enforcing input context quality auditing.

## 0x02 Stage 3 5 Final Context Payload Attestation FCPA

This stage represents the definitive line of defense and circuit breaker before the compiled payload is delivered to the downstream LLM 

Once the Slot Checker evaluates the Completeness Score as higher or equal to 0 85 either via the initial single pass input or after being dynamically funneled through the Question Node the system does not automatically invoke the downstream LLM Instead it enters the FCPA Phase

1 Payload Manifest Generation The framework serializes the fully compiled context matrix including mapped Roles Tasks and high priority Constraints into an human readable Assertion Checklist or a structured prompt preview
2 Zero Friction Self Check Prompt The system delivers a deterministic attestation prompt to the user as follows 
Based on your input the strict execution matrix has been finalized as follows Constraint A Constraint B If this perfectly aligns with your target reply Y or click confirm to execute the payload Otherwise reject to re edit

### Engineering Rationale behind FCPA

Mitigating Semantic Alignment Drift While the mathematical Slot Checker might evaluate the payload as contextually complete with a score higher or equal to 0 85 semantic parsing noise might still cause minor deviations from the user implicit intent FCPA guarantees absolute semantic alignment
Deterministic Token Guard By introducing a physical user confirmation bottleneck the protocol acts as a circuit breaker guaranteeing zero financial and compute waste or token burn caused by executing loose misaligned context payloads on high parameter models

### Core Engine Architecture Python Implementation

```python
# ==============================================================================
#  Dynamic Interactive Context Refinement Protocol (DICRP) - Core Engine
#  
#  Copyright (c) 2026 DICRP Author. All rights reserved.
#  Distributed under the MIT License.
#  
#  Author Identity Reference: JYDS
#  Cryptographic Temporal Provenance: Verified via Immutable Git Commit Timestamp
# ==============================================================================

import json
from typing import Dict, Any, Tuple

class DICRPEngine:
    def __init__(self, slot_schema: Dict[str, float], threshold: float = 0.85):
        self.slot_schema = slot_schema
        self.threshold = threshold
        self.current_slots = {key: None for key in slot_schema.keys()}

    def evaluate_completeness(self) -> float:
        score = 0.0
        for slot, weight in self.slot_schema.items():
            if self.current_slots.get(slot) is not None:
                score += weight
        return round(score, 2)

    def process_input(self, extracted_slots: Dict[str, Any]) -> Tuple[str, Dict[str, Any]]:
        for key, value in extracted_slots.items():
            if key in self.current_slots and value is not None:
                self.current_slots[key] = value

        score = self.evaluate_completeness()
        
        if score >= self.threshold:
            return "ATTESTATION", {
                "score": score, 
                "compiled_manifest": self.current_slots,
                "attestation_prompt": "Verify the execution constraints Reply Y to fire payload"
            }
        else:
            missing_slots = {k: self.slot_schema[k] for k, v in self.current_slots.items() if v is None}
            next_target_slot = max(missing_slots, key=missing_slots.get)
            return "INTERCEPTED", {"score": score, "target_slot": next_target_slot}

    def execute_final_fire(self, user_confirmation: str) -> bool:
        return user_confirmation.strip().upper() == "Y"
