## Optimzer Practice

```
class CustomAdaGrad:
    def __init__(self, params, alpha=0.01, epsilon=1e-8):
        self.params = list(params)
        self.alpha = alpha
        self.epsilon = epsilon
        self.G = [torch.zeros_like(p) for p in self.params]  # Accumulated squared gradients

    def step(self):
        for i, param in enumerate(self.params):
            if param.grad is None:
                continue
               
            # Accumulate squared gradients
            self.G[i] += param.grad ** 2
           
            # Update parameters
            param.data -= self.alpha * param.grad / (torch.sqrt(self.G[i]) + self.epsilon)

    def zero_grad(self):
        for param in self.params:
            if param.grad is not None:
                param.grad.zero_()

class CustomRMSprop:
    def __init__(self, params, alpha=0.001, beta=0.9, epsilon=1e-8):
        self.params = list(params)
        self.alpha = alpha
        self.beta = beta
        self.epsilon = epsilon
        self.v = [torch.zeros_like(p) for p in self.params]  # Moving average of squared gradients

    def step(self):
        for i, param in enumerate(self.params):
            if param.grad is None:
                continue
               
            # Update moving average of squared gradients
            self.v[i] = self.beta * self.v[i] + (1 - self.beta) * param.grad ** 2
           
            # Update parameters
            param.data -= self.alpha * param.grad / (torch.sqrt(self.v[i]) + self.epsilon)

    def zero_grad(self):
        for param in self.params:
            if param.grad is not None:
                param.grad.zero_()

class CustomAdaDelta:
    def __init__(self, params, rho=0.95, epsilon=1e-6):
        self.params = list(params)
        self.rho = rho
        self.epsilon = epsilon
        # Running average of squared gradients
        self.E_g2 = [torch.zeros_like(p) for p in self.params]
        # Running average of squared parameter updates
        self.E_dx2 = [torch.zeros_like(p) for p in self.params]

    def step(self):
        for i, param in enumerate(self.params):
            if param.grad is None:
                continue
               
            # Update running average of squared gradients
            self.E_g2[i] = self.rho * self.E_g2[i] + (1 - self.rho) * param.grad ** 2
           
            # Compute update
            delta = torch.sqrt(self.E_dx2[i] + self.epsilon) / torch.sqrt(self.E_g2[i] + self.epsilon) * param.grad
           
            # Update parameters
            param.data -= delta
           
            # Update running average of squared parameter updates
            self.E_dx2[i] = self.rho * self.E_dx2[i] + (1 - self.rho) * delta ** 2

    def zero_grad(self):
        for param in self.params:
            if param.grad is not None:
                param.grad.zero_()

class CustomAdam:
    def __init__(self, params, alpha=0.0001, beta1=0.85, beta2=0.98, epsilon=1e-8):
        self.params = list(params)
        self.alpha = alpha
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        self.m = [torch.zeros_like(p) for p in self.params]
        self.v = [torch.zeros_like(p) for p in self.params]
        self.t = 0

    def step(self):
        self.t += 1
        for i, param in enumerate(self.params):
            if param.grad is None:
                continue

            # Update biased first moment estimate
            self.m[i] = self.beta1 * self.m[i] + (1 - self.beta1) * param.grad

            # Update biased second raw moment estimate
            self.v[i] = self.beta2 * self.v[i] + (1 - self.beta2) * param.grad ** 2

            # Compute bias-corrected first moment estimate
            m_hat = self.m[i] / (1 - self.beta1 ** self.t)

            # Compute bias-corrected second raw moment estimate
            v_hat = self.v[i] / (1 - self.beta2 ** self.t)

            # Update parameters
            param.data -= self.alpha * m_hat / (torch.sqrt(v_hat) + self.epsilon)

    def zero_grad(self):
        for param in self.params:
            if param.grad is not None:
                param.grad.zero_()

# Training function
def train_model(model, optimizer, criterion, X_train, y_train, X_val, y_val, epochs=1000):
    train_losses = []
    val_losses = []

    for epoch in range(epochs):
        # Training
        model.train()
        optimizer.zero_grad()
        outputs = model(X_train)
        loss = criterion(outputs, y_train)
        loss.backward()
        optimizer.step()
        train_losses.append(loss.item())

        # Validation
        model.eval()
        with torch.no_grad():
            val_outputs = model(X_val)
            val_loss = criterion(val_outputs, y_val)
            val_losses.append(val_loss.item())

        if (epoch + 1) % 10 == 0:
            print(f'Epoch [{epoch+1}/{epochs}], Train Loss: {loss.item():.4f}, Val Loss: {val_loss.item():.4f}')

    return train_losses, val_losses
```