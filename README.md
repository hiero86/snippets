import React, { createContext } from 'react';
import { useToast } from '@/components/ui/use-toast';
import { ToastActionElement } from '@/components/ui/toast';
import { ExternalToast, toast as snackbar } from 'sonner';
import { Toaster } from '@/components/ui/toaster';
import { Toaster as Snackbar } from '@/components/ui/sonner';
 
// Create a context
const AppSnackbarContext = createContext({
  showToast: (options: { title?: string; description?: string; action?: ToastActionElement | undefined }) => {},
  showSnackbar: (title: string, options?: ExternalToast) => {},
});
 
// Create a provider
export function AppSnackbarProvider({ children }: { children: React.ReactNode }) {
  const { toast } = useToast();
 
  const context = {
    showToast: (options: { title?: string; description?: string; action?: ToastActionElement | undefined }) => {
      toast({
        title: options?.title,
        description: options?.description,
        duration: 5000,
        variant: 'default',
        action: options.action,
      });
    },
    showSnackbar: (title: string, options?: ExternalToast) => {
      snackbar(title, {
        ...options,
        duration: options?.duration ?? 5000,
        position: 'bottom-center',
        closeButton: true,
      });
    },
  };
 
  return (
    <AppSnackbarContext.Provider value={context}>
      <>
        {children}
        <Toaster />
        <Snackbar position="bottom-center" />
      </>
    </AppSnackbarContext.Provider>
  );
}
 
// Create a hook that uses the context
export function useSnackbar() {
  const context = React.useContext(AppSnackbarContext);
  if (context === undefined) {
    throw new Error('useSnackbar must be used within a SnackbarProvider');
  }
  return context;
}
