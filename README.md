// src/components/AnimatedSwitch.tsx
import React from 'react';
import { Switch, Route, useLocation } from 'react-router-dom';
import { AnimatePresence, motion } from 'framer-motion';
import { TabContent1 } from './TabContent1';
import { TabContent2 } from './TabContent2';
import { TabContent3 } from './TabContent3';

const pageVariants = {
  initial: (direction: number) => ({
    x: direction > 0 ? 1000 : -1000,
    opacity: 0,
  }),
  in: {
    x: 0,
    opacity: 1,
  },
  out: (direction: number) => ({
    x: direction < 0 ? 1000 : -1000,
    opacity: 0,
  }),
};

const pageTransition = {
  type: 'tween',
  ease: 'anticipate',
  duration: 0.5,
};

export const AnimatedSwitch: React.FC = () => {
  const location = useLocation();
  const { pathname } = location;

  // Determine direction based on previous and current pathnames
  const direction = React.useRef(0);
  React.useEffect(() => {
    direction.current = routes.indexOf(pathname) - routes.indexOf(location.pathname);
  }, [pathname]);

  return (
    <AnimatePresence initial={false} custom={direction.current}>
      <motion.div
        key={location.pathname}
        custom={direction.current}
        variants={pageVariants}
        initial="initial"
        animate="in"
        exit="out"
        transition={pageTransition}
      >
        <Switch location={location} key={location.pathname}>
          <Route path="/tab1" component={TabContent1} />
          <Route path="/tab2" component={TabContent2} />
          <Route path="/tab3" component={TabContent3} />
        </Switch>
      </motion.div>
    </AnimatePresence>
  );
};






// src/components/TabNavigation.tsx
import React from 'react';
import { Tabs, Tab } from '@mui/material';
import { useHistory, useLocation } from 'react-router-dom';

const routes = ['/tab1', '/tab2', '/tab3'];

export const TabNavigation: React.FC = () => {
  const history = useHistory();
  const location = useLocation();

  const handleChange = (event: React.SyntheticEvent, newValue: number) => {
    history.push(routes[newValue]);
  };

  const currentTab = routes.indexOf(location.pathname);

  return (
    <Tabs value={currentTab !== -1 ? currentTab : 0} onChange={handleChange}>
      <Tab label="Tab 1" />
      <Tab label="Tab 2" />
      <Tab label="Tab 3" />
    </Tabs>
  );
};



// src/App.tsx
import React from 'react';
import { BrowserRouter as Router } from 'react-router-dom';
import { TabNavigation } from './components/TabNavigation';
import { AnimatedSwitch } from './components/AnimatedSwitch';

const App: React.FC = () => {
  return (
    <Router>
      <TabNavigation />
      <AnimatedSwitch />
    </Router>
  );
};

export default App;
