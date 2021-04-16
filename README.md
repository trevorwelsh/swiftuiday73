# swiftuiday73

import SwiftUI
import LocalAuthentication

struct ContentView: View {
    @State private var isUnlocked = false
    @State private var unlockError = false
    @State private var unlockErrorMessage = ""
    
    var body: some View {
        ZStack {
            if isUnlocked {
                ConsolidatedMapView()
                
            } else {
                Button("Unlock Places") {
                    self.authenticate()
                }
                .padding()
                .background(Color.gray)
                .foregroundColor(.white)
                .clipShape(Capsule())
            }
        }
        .alert(isPresented: $unlockError) {
            Alert(title: Text("Authentication Error"), message: Text(unlockErrorMessage), dismissButton: .default(Text("Dismiss")))
        }
    }
    
    func authenticate() {
        let context = LAContext()
        var error: NSError?

        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            let reason = "Please authenticate yourself to unlock your places."

            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: reason) { (success, authenticationError) in
                DispatchQueue.main.async {
                    if success {
                        self.isUnlocked = true
                    } else {
                        self.unlockErrorMessage = authenticationError?.localizedDescription ?? "Unknown error"
                        self.unlockError = true
                    }
                }
            }
        } else {
            print("Device does not support biometric authentication.")
        }
    }
}
