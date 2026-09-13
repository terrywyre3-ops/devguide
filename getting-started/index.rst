import time

class Neuron:
    def __init__(self, name, threshold=1.0, decay=0.9):
        self.name = name
        self.voltage = 0.0
        self.threshold = threshold  # Voltage needed to fire
        self.decay = decay          # How fast voltage leaks away over time
        self.connections = []       # List of (downstream_neuron, synaptic_weight)

    def receive_input(self, current):
        self.voltage += current

    def update(self):
        fired = False
        # 1. Check if the neuron spikes
        if self.voltage >= self.threshold:
            print(f"💥 [{self.name}] FIRED a spike!")
            fired = True
            self.voltage = 0.0  # Reset after firing
        else:
            # 2. Voltage decays over time if it doesn't fire (leaky)
            self.voltage *= self.decay
        return fired

# --- Step 1: Set up the Brain Topology ---
eye_neuron = Neuron("Eye Neuron", threshold=1.0)
interneuron = Neuron("Processing Interneuron", threshold=0.8)
motor_neuron = Neuron("Wing Motor Neuron", threshold=0.5)

# --- Step 2: Wire them together (Your downloaded connectome data) ---
# (Downstream Neuron, Synaptic Strength/Weight)
eye_neuron.connections = [(interneuron, 0.9)]
interneuron.connections = [(motor_neuron, 0.6)]

# --- Step 3: Run the Simulation Loop ---
print("Starting Fly Brain Simulation... (Press Ctrl+C to stop)")
try:
    for millisecond in range(1, 11):
        print(f"\n--- Millisecond {millisecond} ---")
        
        # Stimulate the eye neuron on the 1st and 5th millisecond (simulating light flashes)
        if millisecond in:
            print("👁️ Light hits the fly's eye!")
            eye_neuron.receive_input(1.2)
        
        # Track which neurons need to pass signals forward
        signals_to_propagate = []
        
        # Update each neuron and check if it spiked
        for neuron in [eye_neuron, interneuron, motor_neuron]:
            if neuron.update():
                # Queue up signals to downstream neurons
                for target, weight in neuron.connections:
                    signals_to_propagate.append((target, weight))
        
        # Deliver the electrical signals to the downstream neurons for the next millisecond
        for target, weight in signals_to_propagate:
            target.receive_input(weight)
            
        time.sleep(0.5)  # Slow down the output so it's readable

except KeyboardInterrupt:
    print("\nSimulation stopped.")
