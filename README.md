0 2px 4px -1px #0003, 0 4px 5px #00000024, 0 1px 10px #0000001f;

'use client';
import { styled, Theme, CSSObject } from '@mui/material/styles';
import Drawer from '@mui/material/Drawer';
import List from '@mui/material/List';
import ListItem from '@mui/material/ListItem';
import ListItemButton from '@mui/material/ListItemButton';
import ListItemIcon from '@mui/material/ListItemIcon';
import ListItemText from '@mui/material/ListItemText';
import React, { useContext } from 'react';

//import router from nextjs
import { useRouter } from 'next/router';
import Link from 'next/link';
import {
  MdClose,
  MdDashboard,
  MdInbox,
  MdMail,
  MdChevronLeft,
} from 'react-icons/md';
import { SidenavContext } from '@/providers/SidenavProvider';
import { ClassNames } from '@emotion/react';
import Box from '@mui/material/Box';
import { IconButton, Toolbar } from '@mui/material';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';

const drawerWidth = 240;

const container = window !== undefined ? () => document.body : undefined;

const openedMixin = (theme: Theme): CSSObject => ({
  width: drawerWidth,
  transition: theme.transitions.create('width', {
    easing: theme.transitions.easing.sharp,
    duration: theme.transitions.duration.enteringScreen,
  }),
  overflowX: 'hidden',
});

const closedMixin = (theme: Theme): CSSObject => ({
  transition: theme.transitions.create('width', {
    easing: theme.transitions.easing.sharp,
    duration: theme.transitions.duration.leavingScreen,
  }),
  overflowX: 'hidden',
  width: `calc(${theme.spacing(7)} + 1px)`,
  [theme.breakpoints.up('sm')]: {
    width: `calc(${theme.spacing(8)} + 1px)`,
  },
});

const Sidebar = styled(Drawer, {
  shouldForwardProp: (prop) => prop !== 'open',
})(({ theme, open }) => ({
  width: drawerWidth,
  flexShrink: 0,
  whiteSpace: 'nowrap',
  boxSizing: 'border-box',
  backgroundColor: '#374b66',
  ' .MuiDrawer-paper': {
    top: 48,
  },
  ...(open && {
    ...openedMixin(theme),
    '& .MuiDrawer-paper': openedMixin(theme),
  }),
  ...(!open && {
    ...closedMixin(theme),
    '& .MuiDrawer-paper': closedMixin(theme),
  }),
}));

const NavDrawer = styled(Drawer, {
  shouldForwardProp: (prop) => prop !== 'open',
})(({ theme, open }) => ({
  // width: drawerWidth,
  flexShrink: 0,
  whiteSpace: 'nowrap',
  boxSizing: 'border-box',
  ' .MuiDrawer-paper': {
    top: 52,
    paddingTop: 8,
  },
  ...(open && {
    ...openedMixin(theme),
    '& .MuiDrawer-paper': openedMixin(theme),
  }),
  ...(!open && {
    ...closedMixin(theme),
    '& .MuiDrawer-paper': closedMixin(theme),
  }),
}));

{
  /* <Drawer
    container={container}
    variant="temporary"
    open={mobileOpen}
    onClose={handleDrawerToggle}
    ModalProps={{
        keepMounted: true, // Better open performance on mobile.
    }}
    sx={{
        display: { xs: 'block', sm: 'none' },
        '& .MuiDrawer-paper': { boxSizing: 'border-box', width: drawerWidth },
    }}
>
    {drawer}
</Drawer> */
}

const ModalSideNav = styled(Drawer, {
  shouldForwardProp: (prop) => prop !== 'open',
})(({ theme, open }) => ({
  width: drawerWidth,
  flexShrink: 0,
  whiteSpace: 'nowrap',
  boxSizing: 'border-box',
  backgroundColor: '#374b66',
  // ...(open && {
  //     ...openedMixin(theme),
  //     '& .MuiDrawer-paper': openedMixin(theme),
  // }),
  // ...(!open && {
  //     ...closedMixin(theme),
  //     '& .MuiDrawer-paper': closedMixin(theme),
  // }),
}));

const NavList = function ({ open }: { open: boolean }) {
  const pathName = usePathname();
  return (
    <List className="h-full pt-">
      {[
        { path: '/dashboard', label: 'Dashboard', icon: <MdDashboard /> },
        { path: '/workspaces', label: 'Workspaces', icon: <MdMail /> },
        { path: '/organizations', label: 'Organizations', icon: <MdInbox /> },
      ].map((item: any, index) => (
        <ListItem
          key={item.path}
          disablePadding
          sx={{ display: 'block' }}
          className={clsx({
            ' bg-cyan-800 text-white': pathName === item.path,
          })}
        >
          <ListItemButton
            dense={true}
            component={Link}
            href={item.path}
            sx={{
              justifyContent: open ? 'initial' : 'center',
              px: 2.5,
            }}
          >
            <ListItemIcon
              sx={{
                minWidth: 0,
                mr: open ? 3 : 'auto',
                justifyContent: 'center',
                fontSize: '1.5rem',
                color: 'inherit',
              }}
            >
              {item.icon}
            </ListItemIcon>
            <ListItemText primary={item.label} sx={{ opacity: open ? 1 : 0 }} />
          </ListItemButton>
        </ListItem>
      ))}
    </List>
  );
};

export default function Sidenav() {
  const { open, toggleSidenav } = useContext(SidenavContext);

  return (
    <Box>
      <Drawer
        container={container}
        variant="temporary"
        open={open}
        onClose={() => toggleSidenav()}
        ModalProps={{
          keepMounted: true, // Better open performance on mobile.
        }}
        sx={{
          display: { xs: 'block', sm: 'none' },

          '& .MuiDrawer-paper': {
            boxSizing: 'border-box',
            width: drawerWidth,
            backgroundColor: '#374b66',
          },
        }}
      >
        <div className="flex justify-end py-1 px-2">
          <IconButton onClick={() => toggleSidenav()}>
            <MdChevronLeft />
          </IconButton>
        </div>
        <NavList open={open}></NavList>
      </Drawer>

      <NavDrawer
        variant="permanent"
        sx={(theme) => ({
          display: { xs: 'none', sm: 'block' },
          '& .MuiDrawer-paper': {
            boxSizing: 'border-box',
            backgroundColor:
              theme.palette.mode === 'dark' ? '#101418' : '#f5f5f5',
            zIndex: 1001,
            border: 'none',
            boxShadow:
              theme.palette.mode === 'dark'
                ? '2px 0px 3px -1px #000'
                : '0 2px 4px -1px #0003, 0 4px 5px #00000024, 0 1px 10px #0000001f',
          },
        })}
        open={open}
      >
        <NavList open={open}></NavList>
      </NavDrawer>

      {/* <Sidebar variant="permanent" open={open} sx={{ display: { xs: "block", md: "none" } }} >
                <NavList open={open}></NavList>
            </Sidebar>


            <ModalSideNav
                container={container}
                variant="temporary"
                open={open}
                // onClose={handleDrawerToggle}
                ModalProps={{
                    keepMounted: true, // Better open performance on mobile.
                }}
                sx={{
                    display: { xs: 'block', sm: 'none' },
                    '& .MuiDrawer-paper': { boxSizing: 'border-box', width: drawerWidth },
                }}
            >
                <NavList open={open}></NavList>
            </ModalSideNav> */}
    </Box>
  );
}






'use client';
import { createContext, useState } from 'react';

export const SidenavContext = createContext<{
  open: boolean;
  toggleSidenav: () => void;
}>({
  open: false,
  toggleSidenav: () => {},
});

export const SidenavProvider = ({
  children,
}: {
  children: React.ReactNode;
}) => {
  const [open, setOpen] = useState(false);
  const toggleSidenav = () => {
    setOpen(!open);
  };
  return (
    <SidenavContext.Provider value={{ open, toggleSidenav }}>
      {children}
    </SidenavContext.Provider>
  );
};
